# 框架支持矩阵

> 最新发布版本是 **MiniCPM-V 4.6**。下方矩阵优先展示 v4.6 的上游合并状态，v4.5 / o4.5 行作为历史信息保留。

## MiniCPM-V 4.6（最新）

| 分类 | 框架 | 文档 | 上游 PR | 起始分支 | 起始 Release |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 推理 | Transformers | [文档](../../inference/minicpm-v4_6_single_image.md) | [huggingface/transformers](https://github.com/huggingface/transformers/tree/v5.7.0/src/transformers/models/minicpmv4_6)（已合并） | main | [v5.7.0](https://github.com/huggingface/transformers/releases/tag/v5.7.0) |
| 端侧 | llama.cpp | [文档](../../deployment/llama.cpp/minicpm-v4_6_llamacpp.md) | [#22529](https://github.com/ggml-org/llama.cpp/pull/22529)（2026-05-06） | master（2026-05-06） | [b9049](https://github.com/ggml-org/llama.cpp/releases/tag/b9049) |
| 端侧 | Ollama | [文档](../../deployment/ollama/minicpm-v4_6_ollama.md) | 合并中 | 合并中 | 等待官方发布 |
| 云端服务 | vLLM | [文档](../../deployment/vllm/minicpm-v4_6_vllm.md) | [#41254](https://github.com/vllm-project/vllm/pull/41254)（2026-04-29） | 合并中 | 等待官方发布 |
| 云端服务 | SGLang | [文档](../../deployment/sglang/MiniCPM-v4_6_sglang.md) | 合并中 | 合并中 | 等待官方发布 |

## MiniCPM-o 4.5

| 分类 | 框架 | 文档 | 上游 PR | 起始分支 | 起始 Release |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 端侧 | llama.cpp | [文档](../../deployment/llama.cpp/minicpm-o4_5_llamacpp.md) | [#19211](https://github.com/ggml-org/llama.cpp/pull/19211)（2026-01-30） | master（2026-01-30） | [b7895](https://github.com/ggml-org/llama.cpp/releases/tag/b7895) |
| 端侧 | Ollama | [文档](../../deployment/ollama/minicpm-o4_5_ollama.md) | [#12078](https://github.com/ollama/ollama/pull/12078)（2025-08-26） | 合并中 | 等待官方发布 |
| 云端服务 | vLLM | [文档](../../deployment/vllm/minicpm-o4_5_vllm.md) | [#33431](https://github.com/vllm-project/vllm/pull/33431)（2026-01-30） | 合并中 | 等待官方发布 |
| 云端服务 | SGLang | [文档](../../deployment/sglang/MiniCPM-o4_5_sglang.md) | [#9610](https://github.com/sgl-project/sglang/pull/9610)（2025-08-26） | 合并中 | 等待官方发布 |

## 跨版本

| 分类 | 框架 | 文档 | 上游 PR | 起始分支 | 起始 Release |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 微调 | LLaMA-Factory | [文档](../../finetune/finetune_llamafactory.md) | [#9022](https://github.com/hiyouga/LLaMA-Factory/pull/9022)（2025-08-26） | main（2025-08-26） | 等待官方发布 |
| 量化 | GGUF | [文档](../../quantization/gguf/minicpm-v4_6_gguf_quantize.md) | — | — | — |
| 量化 | BNB | [文档](../../quantization/bnb/minicpm-v4_6_bnb_quantize.md) | — | — | — |
| 量化 | AWQ | [文档](../../quantization/awq/minicpm-v4_6_awq_quantize.md) | [tc-mb/AutoAWQ](https://github.com/tc-mb/AutoAWQ) | — | — |
| 演示 | Gradio Demo | [文档](../../demo/web_demo/gradio/README.md) | — | — | — |

如果希望我们优先支持其它开源框架，欢迎通过这个 [简短表单](https://docs.google.com/forms/d/e/1FAIpQLSdyTUrOPBgWqPexs3ORrg47ZcZ1r4vFQaA4ve2iA7L9sMfMWw/viewform) 反馈。
