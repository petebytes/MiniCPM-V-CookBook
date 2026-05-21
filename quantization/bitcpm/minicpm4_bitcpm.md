# BitCPM4 (3-bit Ternary Quantization)

> [!NOTE]
> BitCPM4 is the official **ternary 3-bit** quantization of MiniCPM 4, shipped as part of the MiniCPM 4 release. It compresses the model to roughly 10% of the FP16 size while preserving the bulk of the original capability.

## Models

| Variant | HuggingFace | ModelScope |
| :--- | :--- | :--- |
| BitCPM4 1B | [`openbmb/BitCPM4-1B`](https://huggingface.co/openbmb/BitCPM4-1B) | [mirror](https://www.modelscope.cn/models/OpenBMB/BitCPM4-1B) |
| BitCPM4 0.5B | [`openbmb/BitCPM4-0.5B`](https://huggingface.co/openbmb/BitCPM4-0.5B) | [mirror](https://www.modelscope.cn/models/OpenBMB/BitCPM4-0.5B) |

## When to use it

- Tightly memory-constrained edge deployments (microcontrollers, low-end mobile, embedded SoCs).
- Cases where the 0.5B / 1B size point is already enough — BitCPM4 keeps it small without further quality drop.
- Research on ternary / low-bit inference; weights are public.

If you need an 8B-scale model, BitCPM4 is **not** the right choice — go with AWQ / GPTQ / Marlin INT4 variants of MiniCPM 4 or 4.1 instead.

## Inference (HF Transformers)

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

model_path = "openbmb/BitCPM4-1B"   # or BitCPM4-0.5B

tokenizer = AutoTokenizer.from_pretrained(model_path, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(
    model_path,
    torch_dtype=torch.bfloat16,
    trust_remote_code=True,
).eval().cuda()

messages = [{"role": "user", "content": "Write a short poem about the sea."}]
input_ids = tokenizer.apply_chat_template(
    messages, add_generation_prompt=True, return_tensors="pt",
).to(model.device)
out = model.generate(input_ids, max_new_tokens=256, do_sample=True,
                     temperature=0.7, top_p=0.8)
print(tokenizer.decode(out[0][input_ids.shape[-1]:], skip_special_tokens=True))
```

## Memory footprint

| Model | FP16 size | BitCPM4 size | Reduction |
| :--- | :--- | :--- | :--- |
| MiniCPM4-1B | ~2 GB | ~250 MB | ~88% |
| MiniCPM4-0.5B | ~1 GB | ~130 MB | ~87% |

Actual disk size depends on metadata; runtime VRAM is dominated by activations + KV cache for short prompts.

## Notes

- BitCPM4 uses a custom W3 representation packed into INT8 storage; deserialisation happens inside the HF modelling code.
- For higher throughput, the model is compatible with [CPM.cu](https://github.com/OpenBMB/CPM.cu) — see the deployment guide under MiniCPM 4.
- Reference quality numbers are in the [MiniCPM 4 technical report](https://arxiv.org/abs/2506.07900).
