# MiniCPM4-MCP

> 针对 [Model Context Protocol](https://modelcontextprotocol.io/) 工具调用微调的端侧 LLM agent。基于 MiniCPM 4，在 32 个 MCP 服务器上取得约 76% 任务通过率，与 GPT-4o 量级 agent 持平。

## 模型

| 变体 | HuggingFace |
| :--- | :--- |
| MiniCPM4-MCP | [`openbmb/MiniCPM4-MCP`](https://huggingface.co/openbmb/MiniCPM4-MCP) |

模型基于标准 MiniCPM4-8B backbone，叠加 MCP 风格的工具使用 SFT。

## 什么是 MCP？

[Model Context Protocol](https://modelcontextprotocol.io/) 是连接 LLM 客户端与工具 / 数据服务的开放协议。每个 MCP 服务器通过 JSON schema 描述自己提供的工具；客户端（应用 + LLM）通过结构化 function-call 消息调用。MiniCPM4-MCP 经过专门训练，能可靠地输出这类调用。

## 快速开始（Transformers）

```python
import torch
import json
from transformers import AutoModelForCausalLM, AutoTokenizer

model_path = "openbmb/MiniCPM4-MCP"
tokenizer = AutoTokenizer.from_pretrained(model_path, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(
    model_path, torch_dtype=torch.bfloat16, trust_remote_code=True,
).eval().cuda()

# 工具 schema —— 与 MCP / OpenAI function-call 规范同型
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "查询指定城市当前天气。",
        "parameters": {
            "type": "object",
            "properties": {"city": {"type": "string"}},
            "required": ["city"],
        },
    },
}]

messages = [{"role": "user", "content": "今天北京天气怎么样？"}]

prompt = tokenizer.apply_chat_template(
    messages, tools=tools, add_generation_prompt=True, tokenize=False,
)
input_ids = tokenizer(prompt, return_tensors="pt").input_ids.to(model.device)
out = model.generate(input_ids, max_new_tokens=256, do_sample=False)
reply = tokenizer.decode(out[0][input_ids.shape[-1]:], skip_special_tokens=True)
print(reply)
```

输出通常是结构化的 tool-call 消息。解析后分发到真实工具，再把工具结果作为下一条 `tool` 消息反馈给模型。

## 参考评测

在官方 benchmark 的 32 个 MCP 服务器上：

| Agent | 任务通过率 |
| :--- | :--- |
| GPT-4o（闭源） | 约 80% |
| **MiniCPM4-MCP（8B，端侧）** | **约 76%** |
| 开源 7-8B baseline | 约 45-60% |

数据来自 [MiniCPM 4 技术报告](https://arxiv.org/abs/2506.07900)。

## 后续阅读

- **完整源码与评测脚本**：<https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/MCP>
- **MCP 规范**：<https://modelcontextprotocol.io/>
- **高吞吐服务化**：使用标准 MiniCPM 4 的 [vLLM](../../../deployment/vllm/) 或 [SGLang](../../../deployment/sglang/) 指南加载即可 —— tool-call 解析发生在客户端，服务端不需要特殊参数。
