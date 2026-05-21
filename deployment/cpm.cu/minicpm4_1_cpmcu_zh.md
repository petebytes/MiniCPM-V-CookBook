# MiniCPM 4.1 - CPM.cu（端侧 CUDA）

> [!NOTE]
> [CPM.cu](https://github.com/OpenBMB/CPM.cu) 是 OpenBMB 为 MiniCPM 系列设计的轻量 CUDA 推理框架，原生匹配 MiniCPM 4 / 4.1 的 EAGLE-FRSpec draft 与 [InfLLM-V2](https://github.com/OpenBMB/infllmv2_cuda_impl) 稀疏注意力 kernel，在端侧 GPU 上的吞吐接近优化过的服务端栈。

## 1. 安装 CPM.cu

```bash
git clone https://github.com/OpenBMB/CPM.cu.git
cd CPM.cu
git submodule update --init --recursive
pip install -e .
```

> [!TIP]
> CPM.cu 会编译自带的 CUDA kernel，请确保 `nvcc` 与 CUDA runtime 版本一致。RTX 3060 / 4090 / Jetson Orin 都是常规测试目标。

## 2. 安装 InfLLM-V2 kernel（长上下文需要）

```bash
git clone https://github.com/OpenBMB/infllmv2_cuda_impl.git
cd infllmv2_cuda_impl
pip install -e .
```

## 3. 使用 EAGLE-FRSpec 投机解码

使用为 CPM.cu 适配的 OpenBMB draft：

- [`openbmb/MiniCPM4.1-8B`](https://huggingface.co/openbmb/MiniCPM4.1-8B) —— target
- [`openbmb/MiniCPM4.1-8B-Eagle3`](https://huggingface.co/openbmb/MiniCPM4.1-8B-Eagle3) —— EAGLE3 draft

```python
from cpm_cu import CPMRunner

runner = CPMRunner(
    target_model="openbmb/MiniCPM4.1-8B",
    draft_model="openbmb/MiniCPM4.1-8B-Eagle3",
    spec_method="eagle3",
    num_speculative_tokens=5,
    dtype="bf16",
    max_context_len=131072,
    use_infllm_v2=True,    # 长上下文启用稀疏注意力
)

reply = runner.chat(
    [{"role": "user", "content": "写一篇关于端侧 AI 的短文。"}],
    max_new_tokens=512,
    temperature=0.7,
    top_p=0.8,
)
print(reply)
```

> `CPMRunner` 接口仅为示意。请以你所装版本的 [CPM.cu README](https://github.com/OpenBMB/CPM.cu#readme) 中的具体入口为准。

## 4. 与 MiniCPM 4 配合

MiniCPM 4 请切换到 CPM.cu 适配的 draft：

- [`openbmb/MiniCPM4-8B`](https://huggingface.co/openbmb/MiniCPM4-8B) —— target
- [`openbmb/MiniCPM4-8B-Eagle-FRSpec`](https://huggingface.co/openbmb/MiniCPM4-8B-Eagle-FRSpec) —— FRSpec draft
- [`openbmb/MiniCPM4-8B-Eagle-FRSpec-QAT`](https://huggingface.co/openbmb/MiniCPM4-8B-Eagle-FRSpec-QAT) —— 量化感知变体

## 5. 注意事项

- CPM.cu 是 **OpenBMB 一等代码**，但发布节奏比 vLLM / SGLang 慢。生产环境建议 pin 到已知可用的 commit。
- 生产 GPU 服务通常用 vLLM + EAGLE3 draft（参见 [vLLM 指南](vllm.html)）更易运维。
- CPM.cu + InfLLM-V2 是我们目前唯一推荐用于**消费级 GPU 端侧**长上下文（128K+）推理的方案。
