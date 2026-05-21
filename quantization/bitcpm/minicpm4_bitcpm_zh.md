# BitCPM4（3-bit 三元量化）

> [!NOTE]
> BitCPM4 是 MiniCPM 4 的官方**三元 3-bit** 量化版本，随 MiniCPM 4 发布。模型体积压缩到 FP16 的约 10%，保留绝大部分能力。

## 模型

| 变体 | HuggingFace | ModelScope |
| :--- | :--- | :--- |
| BitCPM4 1B | [`openbmb/BitCPM4-1B`](https://huggingface.co/openbmb/BitCPM4-1B) | [镜像](https://www.modelscope.cn/models/OpenBMB/BitCPM4-1B) |
| BitCPM4 0.5B | [`openbmb/BitCPM4-0.5B`](https://huggingface.co/openbmb/BitCPM4-0.5B) | [镜像](https://www.modelscope.cn/models/OpenBMB/BitCPM4-0.5B) |

## 适用场景

- 内存极度受限的端侧部署（MCU、低端移动设备、嵌入式 SoC）。
- 0.5B / 1B 尺寸已经够用，需要进一步缩小体积但不希望明显掉质量。
- 三元 / 极低比特推理研究，权重已开源。

如果需要 8B 量级模型，BitCPM4 **不适合** —— 请使用 MiniCPM 4 / 4.1 的 AWQ / GPTQ / Marlin INT4 变体。

## 推理（HF Transformers）

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

model_path = "openbmb/BitCPM4-1B"   # 或 BitCPM4-0.5B

tokenizer = AutoTokenizer.from_pretrained(model_path, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(
    model_path,
    torch_dtype=torch.bfloat16,
    trust_remote_code=True,
).eval().cuda()

messages = [{"role": "user", "content": "写一首关于大海的短诗。"}]
input_ids = tokenizer.apply_chat_template(
    messages, add_generation_prompt=True, return_tensors="pt",
).to(model.device)
out = model.generate(input_ids, max_new_tokens=256, do_sample=True,
                     temperature=0.7, top_p=0.8)
print(tokenizer.decode(out[0][input_ids.shape[-1]:], skip_special_tokens=True))
```

## 内存占用

| 模型 | FP16 大小 | BitCPM4 大小 | 缩减 |
| :--- | :--- | :--- | :--- |
| MiniCPM4-1B | 约 2 GB | 约 250 MB | 约 88% |
| MiniCPM4-0.5B | 约 1 GB | 约 130 MB | 约 87% |

实际磁盘占用受元信息影响；短 prompt 下运行时显存主要由激活与 KV 缓存决定。

## 注意事项

- BitCPM4 使用自定义 W3 表示打包到 INT8 存储，反序列化在 HF 建模代码中完成。
- 高吞吐场景兼容 [CPM.cu](https://github.com/OpenBMB/CPM.cu) —— 参考 MiniCPM 4 下的部署指南。
- 参考的精度数据见 [MiniCPM 4 技术报告](https://arxiv.org/abs/2506.07900)。
