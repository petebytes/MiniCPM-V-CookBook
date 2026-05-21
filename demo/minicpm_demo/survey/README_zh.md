# MiniCPM4-Survey

> 基于 MiniCPM 4 的长篇论文综述生成器。采用 **Plan-Retrieve-Write** 框架，产出带检索引文的结构化学术综述。

## 模型

| 变体 | HuggingFace |
| :--- | :--- |
| MiniCPM4-Survey | [`openbmb/MiniCPM4-Survey`](https://huggingface.co/openbmb/MiniCPM4-Survey) |

模型基于 MiniCPM4-8B，针对综述写作与检索增强合成 trace 做了后训练。

## Plan-Retrieve-Write

MiniCPM4-Survey 把"写 X 主题的综述"分解为三阶段：

1. **Plan**：模型生成大纲 —— 章节标题、子章节主题、范围。
2. **Retrieve**：分章节生成检索 query，调用外部检索系统（官方 demo 使用 Web 搜索，可以替换为自定义检索）。
3. **Write**：基于检索到的片段写作每节，逐步插入引文标记。

## 快速开始（Transformers）

最简用法是把综述主题作为 user message 让模型一次性产出大纲与正文。完整的 Plan-Retrieve-Write 流程请使用上游 demo 目录中的编排脚本。

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
    "content": "请撰写一篇关于检索增强语言模型最新进展的综述，至少包含 5 个章节。"
}]

input_ids = tokenizer.apply_chat_template(
    messages, add_generation_prompt=True, return_tensors="pt",
).to(model.device)

out = model.generate(input_ids, max_new_tokens=4096, do_sample=True,
                     temperature=0.6, top_p=0.95)
print(tokenizer.decode(out[0][input_ids.shape[-1]:], skip_special_tokens=True))
```

## 参考评测

在 SurveyBench（[MiniCPM 4 技术报告](https://arxiv.org/abs/2506.07900) 中的内部基准）上，MiniCPM4-Survey 的综述质量分数与 GPT-4o-mini 相当，但只需一个 8B 模型。

## 后续阅读

- **完整实现（检索编排器、评测脚本）**：<https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/SurveyGeneration>
- **超长综述的服务化**：参考 MiniCPM 4 下的 [CPM.cu](../../../deployment/cpm.cu/) 或 [vLLM](../../../deployment/vllm/) 指南 —— 把 `--max-model-len` 提高到 65536+ 以容纳大纲与写作上下文。
