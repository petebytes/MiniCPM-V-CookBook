# MiniCPM LLM Demos

> Reference applications built on top of the MiniCPM 4 / 4.1 LLM series.

This directory hosts navigation pages and minimal runnable snippets for the official MiniCPM LLM demo projects. The full implementations live in the [OpenBMB/MiniCPM](https://github.com/OpenBMB/MiniCPM) main repository under `demo/minicpm4/`.

## LLM demos

| Demo | Description | Upstream |
| :--- | :--- | :--- |
| [**MiniCPM4-MCP**](mcp/README.md) | An on-device LLM agent fine-tuned for [Model Context Protocol](https://modelcontextprotocol.io/) tool calling. Reaches ~76% task pass rate across 32 MCP servers — competitive with GPT-4o-class agents at a fraction of the deployment cost. | [demo/minicpm4/MCP](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/MCP) |
| [**MiniCPM4-Survey**](survey/README.md) | A long-form survey paper generator using a Plan-Retrieve-Write framework. Produces structured academic-style surveys with retrieved citations. | [demo/minicpm4/SurveyGeneration](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/SurveyGeneration) |

Both run on top of [MiniCPM 4](../../docs/pages/minicpm4/overview.md) or [MiniCPM 4.1](../../docs/pages/minicpm4_1/overview.md).

## Other MiniCPM demos in this cookbook

The Cookbook hosts a wider set of demo applications across the V / o / LLM lineup. Use the table below to jump to the right one for your scenario:

| Demo | Path | Best for | Works with |
| :--- | :--- | :--- | :--- |
| **iOS / Android / HarmonyOS** | [`../ios_demo/ios.md`](../ios_demo/ios.md) · [MiniCPM-V-Apps](https://github.com/OpenBMB/MiniCPM-V-Apps) | On-device multimodal inference on mobile devices | MiniCPM-V / o |
| **Gradio web demo** | [`../web_demo/gradio/`](../web_demo/gradio/) | Quick local web UI for image / video chat | MiniCPM-V / o (4.x, 4.5, 4.6) |
| **OpenWebUI** | [`../web_demo/openwebui/`](../web_demo/openwebui/) | Polished general chat UI on top of OpenAI-compatible servers | **Any** MiniCPM model served via vLLM / SGLang / Ollama (incl. MiniCPM 4 / 4.1) |
| **Omni streaming demo** | [`../web_demo/omni_stream/`](../web_demo/omni_stream/) | Real-time multimodal streaming (audio + vision) | MiniCPM-o |
| **WebRTC full-duplex demo** | [`../web_demo/WebRTC_Demo/`](../web_demo/WebRTC_Demo/) | Low-latency, full-duplex video conversations | MiniCPM-o |

If you're deploying a MiniCPM 4 / 4.1 backend and want a friendly chat UI to test it, **OpenWebUI** is the easiest path — point it at your vLLM / SGLang / Ollama OpenAI endpoint and you're done.
