# MiniCPM-V — iOS 部署（MiniCPM-V-Apps）

> **官方仓库：** [OpenBMB/MiniCPM-V-Apps](https://github.com/OpenBMB/MiniCPM-V-Apps)
>
> README：[English](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/README.md) · [**简体中文 README_zh**](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/README_zh.md)

同一仓库内含 **iOS**、**Android**、**HarmonyOS NEXT** 三端 demo，共享仓库根目录的 **`llama.cpp` git submodule**（分支 `Support-iOS-Demo`）。本文侧重 **iOS**；安卓与鸿蒙的编译说明请直接看 upstream README。

**预编译安装包**（TestFlight / APK / HAP）：[**DOWNLOAD.md**](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/DOWNLOAD.md) / [**DOWNLOAD_zh.md**](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/DOWNLOAD_zh.md)。下面内容为从源码自行构建时使用。

---

## 1. 克隆仓库与子模块

```bash
git clone https://github.com/OpenBMB/MiniCPM-V-Apps.git
cd MiniCPM-V-Apps
git submodule update --init --recursive
```

## 2. 打开 Xcode 工程

**说明：**在真机（iPhone / iPad）上部署、调试通常需要有效的 **Apple Developer** 帐号。

安装 Xcode：

* 从 App Store 安装 Xcode
* 安装命令行工具：

  ```bash
  xcode-select --install
  ```

* 同意许可协议：

  ```bash
  sudo xcodebuild -license
  ```

使用 Xcode 打开 **`MiniCPM-V-demo/MiniCPM-V-demo.xcodeproj`**，等待 Xcode 自动下载所需依赖；在顶部选好运行目标设备，点击 **Run**（三角形）。

**说明：**若出现 **`thirdparty/llama.xcframework`** 相关报错，按第 3 节手动构建框架。

---

## 3. 手动构建 llama.xcframework

在**仓库根目录**（并已拉取 submodule）执行：

```bash
cd llama.cpp
./build-xcframework.sh
cp -r ./build-apple/llama.xcframework ../MiniCPM-V-demo/thirdparty
```

---

## 4. App 所用 GGUF 模型

当前 Demo 对齐 **MiniCPM-V 2.6 / 4.0 / 4.6**。每个版本都需要分别从官方 GGUF 仓下载 **语言模型 GGUF** + 视觉投影器 **`mmproj-model-f16.gguf`**（视觉塔保留 f16 精度，避免感知质量损失）。

**推荐设备内存与总下载体量**（详见 upstream [README_zh 硬件说明](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/README_zh.md#硬件要求)）：

| 模型 | LLM 参数量 | 推荐量化 | 总下载量 | 推荐设备内存 |
| --- | --- | --- | --- | --- |
| MiniCPM-V 2.6 | 8B | Q4\_K\_M | ~5.4 GB | **≥ 8 GB** |
| MiniCPM-V 4.0 | 4.1B | Q4\_K\_M | ~2.9 GB | **≥ 6 GB** |
| MiniCPM-V 4.6 | 1.3B | Q4\_K\_M | ~1.6 GB | **≥ 6 GB** |

三端 demo 默认上下文为 4K token，KV cache 占用近似随上下文线性增长，临界设备上可适当下调。

### MiniCPM-V 2.6 — 官方 GGUF

* Hugging Face：[openbmb/MiniCPM-V-2_6-gguf](https://huggingface.co/openbmb/MiniCPM-V-2_6-gguf)
* 魔搭：[OpenBMB/MiniCPM-V-2_6-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-2_6-gguf)

下载语言模型文件（如 `ggml-model-Q4_0.gguf`）与视觉模型文件 `mmproj-model-f16.gguf`。

### MiniCPM-V 4.0 — 官方 GGUF

* Hugging Face：[openbmb/MiniCPM-V-4-gguf](https://huggingface.co/openbmb/MiniCPM-V-4-gguf)
* 魔搭：[OpenBMB/MiniCPM-V-4-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-4-gguf)

下载语言模型文件（如 `ggml-model-Q4_K_M.gguf`）与视觉模型文件 `mmproj-model-f16.gguf`。

### MiniCPM-V 4.6 — 官方 GGUF

* Hugging Face：[openbmb/MiniCPM-V-4.6-gguf](https://huggingface.co/openbmb/MiniCPM-V-4.6-gguf)
* 魔搭：[OpenBMB/MiniCPM-V-4.6-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-4.6-gguf)

下载语言模型文件（如 `MiniCPM-V-4_6-Q4_K_M.gguf`）与视觉模型文件 `mmproj-model-f16.gguf`。

---

## 5. 从 PyTorch 自行转换 GGUF（可选）

Cookbook 中的转换流程见：

* [MiniCPM-V 4.6 GGUF](../../quantization/gguf/minicpm-v4_6_gguf_quantize_zh.md)
* [MiniCPM-V 4.0 GGUF](https://github.com/OpenSQZ/MiniCPM-V-CookBook/blob/main/quantization/gguf/minicpm-v4_gguf_quantize_zh.md)
* 其他版本：[Cookbook 仓库 `quantization/gguf/` 目录](https://github.com/OpenSQZ/MiniCPM-V-CookBook/tree/main/quantization/gguf)

请在 MiniCPM-V-Apps 仓库根目录下的 **`llama.cpp`** 子模块内执行各文档中的命令。
