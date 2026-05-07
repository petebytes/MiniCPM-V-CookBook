# Framework Support Matrix

> The latest release is **MiniCPM-V 4.6**. The matrix below tracks the upstream merge status of v4.6, with v4.5 / o4.5 rows kept for reference.

## MiniCPM-V 4.6 (latest)

| Category | Framework | Cookbook | Upstream PR | Branch | Release |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Inference | Transformers | [Doc](../../inference/minicpm-v4_6_single_image.md) | [huggingface/transformers](https://github.com/huggingface/transformers/tree/v5.7.0/src/transformers/models/minicpmv4_6) (merged) | main | [v5.7.0](https://github.com/huggingface/transformers/releases/tag/v5.7.0) |
| Edge (On-device) | llama.cpp | [Doc](../../deployment/llama.cpp/minicpm-v4_6_llamacpp.md) | [#22529](https://github.com/ggml-org/llama.cpp/pull/22529) (2026-05-06) | master (2026-05-06) | [b9049](https://github.com/ggml-org/llama.cpp/releases/tag/b9049) |
| Edge (On-device) | Ollama | [Doc](../../deployment/ollama/minicpm-v4_6_ollama.md) | Merging | Merging | Waiting for official release |
| Serving (Cloud) | vLLM | [Doc](../../deployment/vllm/minicpm-v4_6_vllm.md) | [#41254](https://github.com/vllm-project/vllm/pull/41254) (2026-04-29) | Merging | Waiting for official release |
| Serving (Cloud) | SGLang | [Doc](../../deployment/sglang/MiniCPM-v4_6_sglang.md) | Merging | Merging | Waiting for official release |

## MiniCPM-o 4.5

| Category | Framework | Cookbook | Upstream PR | Branch | Release |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Edge (On-device) | llama.cpp | [Doc](../../deployment/llama.cpp/minicpm-o4_5_llamacpp.md) | [#19211](https://github.com/ggml-org/llama.cpp/pull/19211) (2026-01-30) | master (2026-01-30) | [b7895](https://github.com/ggml-org/llama.cpp/releases/tag/b7895) |
| Edge (On-device) | Ollama | [Doc](../../deployment/ollama/minicpm-o4_5_ollama.md) | [#12078](https://github.com/ollama/ollama/pull/12078) (2025-08-26) | Merging | Waiting for official release |
| Serving (Cloud) | vLLM | [Doc](../../deployment/vllm/minicpm-o4_5_vllm.md) | [#33431](https://github.com/vllm-project/vllm/pull/33431) (2026-01-30) | Merging | Waiting for official release |
| Serving (Cloud) | SGLang | [Doc](../../deployment/sglang/MiniCPM-o4_5_sglang.md) | [#9610](https://github.com/sgl-project/sglang/pull/9610) (2025-08-26) | Merging | Waiting for official release |

## Cross-version

| Category | Framework | Cookbook | Upstream PR | Branch | Release |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Finetuning | LLaMA-Factory | [Doc](../../finetune/finetune_llamafactory.md) | [#9022](https://github.com/hiyouga/LLaMA-Factory/pull/9022) (2025-08-26) | main (2025-08-26) | Waiting for official release |
| Quantization | GGUF | [Doc](../../quantization/gguf/minicpm-v4_6_gguf_quantize.md) | — | — | — |
| Quantization | BNB | [Doc](../../quantization/bnb/minicpm-v4_6_bnb_quantize.md) | — | — | — |
| Quantization | AWQ | [Doc](../../quantization/awq/minicpm-v4_6_awq_quantize.md) | [tc-mb/AutoAWQ](https://github.com/tc-mb/AutoAWQ) | — | — |
| Demos | Gradio Demo | [Doc](../../demo/web_demo/gradio/README.md) | — | — | — |

If you'd like us to prioritize support for another open-source framework, please let us know via this [short form](https://docs.google.com/forms/d/e/1FAIpQLSdyTUrOPBgWqPexs3ORrg47ZcZ1r4vFQaA4ve2iA7L9sMfMWw/viewform).
