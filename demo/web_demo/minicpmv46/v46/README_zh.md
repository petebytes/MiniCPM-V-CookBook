# MiniCPM-V 4.6 Gradio 演示

**MiniCPM-V 4.6** 的单进程 Gradio 演示。一个进程可以同时加载 `instruct` 和 `thinking` 两个检查点；通过界面上的"思考模式"开关即可实时切换当前使用的模型（并在 chat template 中切换 `enable_thinking`）。

与 v4.5 演示（使用 FastAPI `server` + Gradio `client` 分离架构以及 `model.chat(...)` 自定义 API）不同，v4.6 使用标准的 HuggingFace transformers API：

```python
from transformers import AutoProcessor, MiniCPMV4_6ForConditionalGeneration

processor = AutoProcessor.from_pretrained(path)
model     = MiniCPMV4_6ForConditionalGeneration.from_pretrained(path, dtype=torch.bfloat16)

inputs = processor.apply_chat_template(messages, add_generation_prompt=True,
                                       tokenize=True, return_dict=True,
                                       return_tensors="pt").to(model.device)
out = model.generate(**inputs, max_new_tokens=..., do_sample=...)
text = processor.decode(out[0, inputs["input_ids"].shape[1]:], skip_special_tokens=True)
```

流式输出通过 `transformers.TextIteratorStreamer` 在后台线程中实现，使 Gradio 界面能逐 token 输出。

## 目录结构

```
gradio/v46/
├── app.py          # 单进程 Gradio 应用（加载 1 或 2 个检查点）
├── start.sh        # tmux 启动器（双模型 / instruct / thinking 模式，负载均衡注册）
├── requirements.txt
└── README.md
```

## 模型

| 模型      | 路径                                                                    |
| --------- | ----------------------------------------------------------------------- |
| instruct  | `./minicpm-v-4_6-0420-rlaif-instruct`     |
| thinking  | `./minicpm-v-4_6-0420-rlaif-thinking`     |

注意：两个模型的 `config.json` 均已添加 `image_token_id: 248056`（即 `<|image_pad|>` 特殊 token 的 id）——缺少此项会导致模型在 `get_placeholder_mask` 中抛出 `AttributeError: 'bool' object has no attribute 'sum'`。

## 环境配置

从 `omni` 克隆一个专用的 `v46` conda 环境：

```bash
conda create --clone omni -n v46 --offline
conda activate v46

PYTHONNOUSERSITE=1 pip install -e ./new-model-addition-MiniCPM-V-4.6 --no-deps
PYTHONNOUSERSITE=1 pip install -U "huggingface_hub>=1.0" "tokenizers>=0.22.0,<=0.23.0" "regex>=2025.10.22" "mistral_common>=1.11.0"
```

需要设置 `PYTHONNOUSERSITE=1`，因为该服务器上的包会覆盖 conda 环境中的 `transformers` 和 `huggingface_hub`，导致版本不兼容。

还需要对环境中的 `src/transformers/models/minicpmv4_6/configuration_minicpmv4_6.py` 进行一个小补丁：

```python
# 修改前：
merge_kernel_size: list[int] = [2, 2]
# 修改后：
from dataclasses import field
merge_kernel_size: list[int] = field(default_factory=lambda: [2, 2])
```

（Python 3.10+ 的 `@dataclass` 不允许使用可变列表作为默认值。）

## 思考模式切换原理

```
  ┌─────────────────────────────────────────────┐
  │  单个 app.py 进程（同一个 CUDA 设备）          │
  │                                             │
  │   MODELS = {                                │
  │     "instruct":  <MiniCPMV4_6…instruct>     │
  │     "thinking":  <MiniCPMV4_6…thinking>     │
  │   }                                         │
  └─────────────────────────────────────────────┘

  开关关闭  →  variant="instruct",  enable_thinking=False
  开关开启  →  variant="thinking",  enable_thinking=True
```

切换开关时，对话历史会自动清除并显示提示 `Switched to 'thinking' model, history cleared.`。清除历史是因为两个检查点的输出风格不同（`<think>…</think>` vs 直接回答），混合在同一对话中会在后续轮次中干扰模型。

显存占用：每个检查点在 bfloat16 下约 16 GB，因此双模型进程需要约 32 GB。推荐在 80 GB 的 A100/H100 上运行。显存较小的显卡可以使用 `--variant instruct` 或 `--variant thinking` 只加载一个检查点。

## 启动方式

### A. 快速启动 — 单 GPU 上运行一个双模型实例

```bash
cd ./MiniCPM-o-demo-web/gradio/v46

# 在 GPU 7 上运行双模型，端口 8890，不使用负载均衡
bash start.sh -n 1 --gpu-start 7 --port-base 8890 --no-lb
```

访问 `http://<host>:8890`，通过"思考模式"开关切换检查点。

### B. 生产部署 — 多个双模型实例 + 负载均衡

架构：

```
                  ┌──────────────────────────────────────────┐
      用户 ─────▶  │  load_balancer :8121  (ip_hash + SSE)    │
                  └──┬───────────────┬──────────────┬────────┘
                     │               │              │
                     ▼               ▼              ▼
              127.0.0.1:8890  127.0.0.1:8891  127.0.0.1:8892     ← v4.6 app.py（双模型）
              (GPU 7)         (GPU 6)         (GPU 5)
                 │               │              │
                 └─── 每个进程同时持有 instruct + thinking ───
```

由于每个后端都提供两种变体，因此**只需一个负载均衡池**，ip_hash 会话粘性确保每个用户始终访问同一个后端（保持开关状态一致）。

#### 1) 启动负载均衡器

```bash
cd ../load_balancer
python load_balancer.py --port 8121 --strategy ip_hash
```

#### 2) 启动 Gradio 实例（自动注册到负载均衡器）

```bash
cd ../v46

# 在 GPU 7,6,5,4 上启动 4 个双模型实例 → 负载均衡器 :8121
bash start.sh -n 4 \
    --gpu-start 7 --port-base 8890 \
    --lb-host 127.0.0.1 --lb-port 8121
```

用户访问：`http://<host>:8121`。

#### 3) 状态查看 / 停止

```bash
bash start.sh --status
bash start.sh --stop        # 同时从负载均衡器注销
```

### C. 单变体集群（降低每个进程的显存占用）

如果显卡无法容纳约 32 GB，可以分别部署两种变体。此模式下"思考模式"开关仅切换 `enable_thinking`（无法切换模型，因为只加载了一个），需要两个负载均衡端口以避免用户误访问另一个模型。

```bash
bash start.sh -n 4 --variant instruct  --gpu-start 7 --port-base 8890 --lb-port 8121
bash start.sh -n 4 --variant thinking  --gpu-start 3 --port-base 8900 --lb-port 8122
```

### D. 手动运行 app.py（不使用 tmux 和负载均衡）

```bash
conda activate v46

# 双模型（开关切换模型）
PYTHONNOUSERSITE=1 CUDA_VISIBLE_DEVICES=7 python app.py \
    --instruct_path=minicpm-v-4_6-0420-rlaif-instruct \
    --thinking_path=minicpm-v-4_6-0420-rlaif-thinking \
    --port=8890

# 单模型
PYTHONNOUSERSITE=1 CUDA_VISIBLE_DEVICES=7 python app.py \
    --instruct_path=minicpm-v-4_6-0420-rlaif-instruct \
    --port=8890
```

### 多机部署

与 v4.5 相同。在主节点上运行负载均衡器，然后在每台工作节点上：

```bash
bash start.sh -n K --lb-host <主节点IP> --lb-port 8121 --local-ip <本机IP>
```

## 界面功能

- **上传**：每轮可上传单张或多张图片，或一个视频。不支持同时上传图片和视频（后端强制检查）。
- **解码方式**：Beam Search（确定性，`num_beams=1`）或采样。
- **思考模式**：加载双模型时切换活跃的检查点；仅加载单模型时切换 `enable_thinking`。切换时会清除对话历史以避免混合不同输出风格。
- **流式输出**：通过 `TextIteratorStreamer` 逐 token 更新。Beam Search 会自动禁用流式输出。
- **参数滑块**：`max_new_tokens`、`temperature`、`top_p`、`top_k`。
- **操作按钮**：重新生成 / 清除历史 / 停止。
- `<think>…</think>` 段落以蓝色卡片形式渲染在回答上方，便于实时查看模型的推理过程。

## 与 v4.5 演示的主要区别

| 方面                  | v4.5                                                  | v4.6                                                              |
| --------------------- | ----------------------------------------------------- | ----------------------------------------------------------------- |
| 架构                  | FastAPI server + Gradio client + LB                   | 单进程 Gradio 应用（可选负载均衡）                                  |
| 每进程模型数           | 1                                                     | **1 或 2**（双模型 instruct+thinking）                              |
| 模型加载              | `AutoModel.from_pretrained(trust_remote_code=True)`   | `MiniCPMV4_6ForConditionalGeneration.from_pretrained(...)`        |
| 推理调用              | `model.chat(msgs, tokenizer, processor, ...)`         | `model.generate(**processor.apply_chat_template(...))`            |
| 视频编码              | 客户端预提取帧 → base64 逐帧 POST                      | Processor 从本地路径内部提取帧                                      |
| 流式输出              | 自定义 `chat(stream=True)` → SSE over HTTP             | `TextIteratorStreamer` 线程 → Gradio 直接 yield                    |
| 思考模式              | `enable_thinking` 传给 `model.chat`                    | `enable_thinking` 传给 `apply_chat_template` + 模型切换             |
