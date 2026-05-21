# MiniCPM4-MCP

> An on-device LLM agent fine-tuned for [Model Context Protocol](https://modelcontextprotocol.io/) tool calling. Built on MiniCPM 4, it reaches ~76% task pass rate across 32 MCP servers — competitive with GPT-4o-class agents.

## Model

| Variant | HuggingFace |
| :--- | :--- |
| MiniCPM4-MCP | [`openbmb/MiniCPM4-MCP`](https://huggingface.co/openbmb/MiniCPM4-MCP) |

The model is the standard MiniCPM4-8B backbone with extra MCP-style tool-use SFT on top.

## What is MCP?

[Model Context Protocol](https://modelcontextprotocol.io/) is an open spec for connecting LLM clients to tool/data servers. Each MCP server exposes a set of tools described by JSON schemas; the client (your application + an LLM) calls them through structured function-call messages. MiniCPM4-MCP is trained to emit those calls reliably.

## Quick start (Transformers)

```python
import torch
import json
from transformers import AutoModelForCausalLM, AutoTokenizer

model_path = "openbmb/MiniCPM4-MCP"
tokenizer = AutoTokenizer.from_pretrained(model_path, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(
    model_path, torch_dtype=torch.bfloat16, trust_remote_code=True,
).eval().cuda()

# Tool schema — same shape as the MCP / OpenAI function-call spec
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get the current weather for a given city.",
        "parameters": {
            "type": "object",
            "properties": {"city": {"type": "string"}},
            "required": ["city"],
        },
    },
}]

messages = [{"role": "user", "content": "What's the weather like in Beijing today?"}]

prompt = tokenizer.apply_chat_template(
    messages, tools=tools, add_generation_prompt=True, tokenize=False,
)
input_ids = tokenizer(prompt, return_tensors="pt").input_ids.to(model.device)
out = model.generate(input_ids, max_new_tokens=256, do_sample=False)
reply = tokenizer.decode(out[0][input_ids.shape[-1]:], skip_special_tokens=True)
print(reply)
```

A typical reply is a structured tool-call message. Parse it, dispatch to the real tool, and feed the tool result back as the next `tool` message.

## Reference benchmark

Across 32 MCP servers from the official benchmark:

| Agent | Task pass rate |
| :--- | :--- |
| GPT-4o (proprietary) | ~80% |
| **MiniCPM4-MCP (8B, on-device)** | **~76%** |
| Open-source 7-8B baselines | ~45-60% |

Numbers from the [MiniCPM 4 technical report](https://arxiv.org/abs/2506.07900).

## Where to go next

- **Full source code & evaluation scripts**: <https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/MCP>
- **MCP spec**: <https://modelcontextprotocol.io/>
- **High-throughput serving**: load the model via [vLLM](../../../deployment/vllm/) or [SGLang](../../../deployment/sglang/) using the standard MiniCPM 4 guides — tool-calling parsing happens client-side, no special server flags needed.
