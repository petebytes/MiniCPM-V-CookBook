# MiniCPM LLM 应用

> 基于 MiniCPM 4 / 4.1 LLM 系列构建的官方参考应用。

本目录提供 MiniCPM LLM 官方应用的导航页与最小可运行片段。完整实现位于 [OpenBMB/MiniCPM](https://github.com/OpenBMB/MiniCPM) 主仓库的 `demo/minicpm4/` 目录。

## 已收录应用

| 应用 | 描述 | 上游链接 |
| :--- | :--- | :--- |
| [**MiniCPM4-MCP**](mcp/README.md) | 针对 [Model Context Protocol](https://modelcontextprotocol.io/) 工具调用微调的端侧 LLM agent，在 32 个 MCP 服务器上取得约 76% 的任务通过率 —— 在部署成本仅为同类的一小部分时，与 GPT-4o 量级 agent 持平。 | [上游 demo 目录](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/MCP) |
| [**MiniCPM4-Survey**](survey/README.md) | 长篇论文综述生成器，采用 Plan-Retrieve-Write 框架，产出带检索引文的结构化学术综述。 | [上游 demo 目录](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/SurveyGeneration) |

两个应用都建立在 [MiniCPM 4](../../docs/pages/minicpm4/overview.md) 或 [MiniCPM 4.1](../../docs/pages/minicpm4_1/overview.md) 之上。多模态形态的应用请参考仓库根目录下的 V/o demo 目录。
