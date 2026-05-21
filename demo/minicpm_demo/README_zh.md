# MiniCPM LLM 应用

> 基于 MiniCPM 4 / 4.1 LLM 系列构建的官方参考应用。

本目录提供 MiniCPM LLM 官方应用的导航页与最小可运行片段。完整实现位于 [OpenBMB/MiniCPM](https://github.com/OpenBMB/MiniCPM) 主仓库的 `demo/minicpm4/` 目录。

## LLM 应用

| 应用 | 描述 | 上游 |
| :--- | :--- | :--- |
| [**MiniCPM4-MCP**](mcp/README.md) | 针对 [Model Context Protocol](https://modelcontextprotocol.io/) 工具调用微调的端侧 LLM agent，在 32 个 MCP 服务器上取得约 76% 的任务通过率 —— 在部署成本仅为同类的一小部分时，与 GPT-4o 量级 agent 持平。 | [demo/minicpm4/MCP](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/MCP) |
| [**MiniCPM4-Survey**](survey/README.md) | 长篇论文综述生成器，采用 Plan-Retrieve-Write 框架，产出带检索引文的结构化学术综述。 | [demo/minicpm4/SurveyGeneration](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/SurveyGeneration) |

两者都建立在 [MiniCPM 4](../../docs/pages/minicpm4/overview.md) 或 [MiniCPM 4.1](../../docs/pages/minicpm4_1/overview.md) 之上。

## Cookbook 中的其他 MiniCPM 应用

本 Cookbook 收录了覆盖 V / o / LLM 全线的应用。下表帮你按场景跳到对应 demo：

| 应用 | 路径 | 适用场景 | 适配模型 |
| :--- | :--- | :--- | :--- |
| **iOS / Android / HarmonyOS** | [`../ios_demo/ios.md`](../ios_demo/ios.md) · [MiniCPM-V-Apps](https://github.com/OpenBMB/MiniCPM-V-Apps) | 移动端端侧多模态推理 | MiniCPM-V / o |
| **Gradio web demo** | [`../web_demo/gradio/`](../web_demo/gradio/) | 本地快速搭建图像 / 视频对话 web UI | MiniCPM-V / o（4.x、4.5、4.6） |
| **OpenWebUI** | [`../web_demo/openwebui/`](../web_demo/openwebui/) | 基于 OpenAI 兼容服务的精致通用聊天 UI | **任意** 通过 vLLM / SGLang / Ollama 服务化的 MiniCPM 模型（含 MiniCPM 4 / 4.1） |
| **Omni 流式 demo** | [`../web_demo/omni_stream/`](../web_demo/omni_stream/) | 实时多模态流（音频 + 视觉） | MiniCPM-o |
| **WebRTC 全双工 demo** | [`../web_demo/WebRTC_Demo/`](../web_demo/WebRTC_Demo/) | 低延迟全双工视频对话 | MiniCPM-o |

如果你已经部署了 MiniCPM 4 / 4.1 后端，想给它配一个友好的聊天 UI，**OpenWebUI 是最快的路径** —— 把它指向 vLLM / SGLang / Ollama 的 OpenAI 接口即可。
