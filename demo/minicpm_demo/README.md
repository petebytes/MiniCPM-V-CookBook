# MiniCPM LLM Demos

> Reference applications built on top of the MiniCPM 4 / 4.1 LLM series.

This directory hosts navigation pages and minimal runnable snippets for the official MiniCPM LLM demo projects. The full implementations live in the [OpenBMB/MiniCPM](https://github.com/OpenBMB/MiniCPM) main repository under `demo/minicpm4/`.

## Available demos

| Demo | Description | Quick link |
| :--- | :--- | :--- |
| [**MiniCPM4-MCP**](mcp/README.md) | An on-device LLM agent fine-tuned for [Model Context Protocol](https://modelcontextprotocol.io/) tool calling. Reaches ~76% task pass rate across 32 MCP servers — competitive with GPT-4o-class agents at a fraction of the deployment cost. | [Upstream demo dir](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/MCP) |
| [**MiniCPM4-Survey**](survey/README.md) | A long-form survey paper generator using a Plan-Retrieve-Write framework. Produces structured academic-style surveys with retrieved citations. | [Upstream demo dir](https://github.com/OpenBMB/MiniCPM/tree/main/demo/minicpm4/SurveyGeneration) |

Both demos run on top of [MiniCPM 4](../../docs/pages/minicpm4/overview.md) or [MiniCPM 4.1](../../docs/pages/minicpm4_1/overview.md). For multimodal-flavoured demos see the V/o demo directories at the repository root.
