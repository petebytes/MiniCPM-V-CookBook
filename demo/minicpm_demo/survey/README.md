# MiniCPM4-Survey

> A long-form survey-paper generator built on MiniCPM 4. Uses a **Plan-Retrieve-Write** framework to produce structured academic-style surveys with retrieved citations.

## Model

| Variant | HuggingFace |
| :--- | :--- |
| MiniCPM4-Survey | [`openbmb/MiniCPM4-Survey`](https://huggingface.co/openbmb/MiniCPM4-Survey) |

The base is MiniCPM4-8B, post-trained on survey writing and retrieval-augmented synthesis traces.

## Plan-Retrieve-Write

MiniCPM4-Survey decomposes "write a survey on X" into three stages:

1. **Plan** — the model outlines the survey: section titles, subsection topics, scope.
2. **Retrieve** — per section, the model issues search queries against an external retrieval system (the official demo uses Web search; you can plug in your own).
3. **Write** — the model writes each section conditioned on the retrieved snippets, inserting citation markers as it goes.

## Quick start (Transformers)

The simplest usage is to call the model with a survey topic and let it produce the plan + body in one go. For the full Plan-Retrieve-Write loop, use the orchestration scripts in the upstream demo directory.

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

model_path = "openbmb/MiniCPM4-Survey"
tokenizer = AutoTokenizer.from_pretrained(model_path, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(
    model_path, torch_dtype=torch.bfloat16, trust_remote_code=True,
).eval().cuda()

messages = [{
    "role": "user",
    "content": "Write a survey about recent advances in retrieval-augmented language models. Cover at least 5 sections."
}]

input_ids = tokenizer.apply_chat_template(
    messages, add_generation_prompt=True, return_tensors="pt",
).to(model.device)

out = model.generate(input_ids, max_new_tokens=4096, do_sample=True,
                     temperature=0.6, top_p=0.95)
print(tokenizer.decode(out[0][input_ids.shape[-1]:], skip_special_tokens=True))
```

## Reference benchmark

On SurveyBench (an internal benchmark from the [MiniCPM 4 technical report](https://arxiv.org/abs/2506.07900)), MiniCPM4-Survey reaches survey-quality scores comparable to GPT-4o-mini while running on a single 8B model.

## Where to go next

- **Full implementation (retrieval orchestrator, evaluation harness)**: <https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/SurveyGeneration>
- **Long-context serving for very long surveys**: see the [CPM.cu](../../../deployment/cpm.cu/) or [vLLM](../../../deployment/vllm/) guides under MiniCPM 4 — bump `--max-model-len` to 65536+ to fit the planning + writing context together.
