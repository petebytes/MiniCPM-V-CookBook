# MiniCPM-V — iOS deployment (MiniCPM-V-Apps)

> **Upstream project:** [OpenBMB/MiniCPM-V-Apps](https://github.com/OpenBMB/MiniCPM-V-Apps)
>
> README: [English](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/README.md) · [简体中文](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/README_zh.md)

That repository bundles **iOS**, **Android**, and **HarmonyOS NEXT** demos sharing one root **`llama.cpp` git submodule** (branch `Support-iOS-Demo`). This page summarizes the **iOS** flow; Android / HarmonyOS build steps stay in the upstream README.

**Prebuilt installers** (TestFlight / APK / HAP): [**DOWNLOAD.md**](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/DOWNLOAD.md) / [**DOWNLOAD_zh.md**](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/DOWNLOAD_zh.md). The sections below are for building from source.

---

## 1. Clone repo and submodule

```bash
git clone https://github.com/OpenBMB/MiniCPM-V-Apps.git
cd MiniCPM-V-Apps
git submodule update --init --recursive
```

## 2. Open the Xcode project

**NOTE:** Deploying on a physical iPhone or iPad may require an Apple Developer membership.

Install Xcode:

* Xcode from the App Store
* Command Line Tools:

  ```bash
  xcode-select --install
  ```

* Accept the license:

  ```bash
  sudo xcodebuild -license
  ```

Open **`MiniCPM-V-demo/MiniCPM-V-demo.xcodeproj`** in Xcode and let it finish downloading the required dependencies. Then pick a destination device at the top and tap **Run**.

**NOTE:** If something fails related to **`thirdparty/llama.xcframework`**, build it manually using section 3.

---

## 3. Manually build `llama.xcframework`

From the **repository root** (after submodules are in place):

```bash
cd llama.cpp
./build-xcframework.sh
cp -r ./build-apple/llama.xcframework ../MiniCPM-V-demo/thirdparty
```

---

## 4. GGUF model files for the demos

Upstream currently targets **MiniCPM-V 2.6**, **4.0**, and **4.6**. For each version, download a quantized **language-model GGUF** plus **`mmproj-model-f16.gguf`** (the vision projector is kept at f16 since quantizing the visual tower hurts perception quality noticeably more than quantizing the LLM).

**Recommended device RAM and total download size** (see upstream [README hardware section](https://github.com/OpenBMB/MiniCPM-V-Apps/blob/main/README.md#hardware-requirements)):

| Model | LLM params | Recommended quant | Total download | Recommended device RAM |
| --- | --- | --- | --- | --- |
| MiniCPM-V 2.6 | 8B | Q4\_K\_M | ~5.4 GB | **≥ 8 GB** |
| MiniCPM-V 4.0 | 4.1B | Q4\_K\_M | ~2.9 GB | **≥ 6 GB** |
| MiniCPM-V 4.6 | 1.3B | Q4\_K\_M | ~1.6 GB | **≥ 6 GB** |

All three demos default to a 4K context window; KV-cache footprint grows roughly linearly with context, so on a borderline device you may want to lower it.

### MiniCPM-V 2.6 — official GGUF

* Hugging Face: [openbmb/MiniCPM-V-2_6-gguf](https://huggingface.co/openbmb/MiniCPM-V-2_6-gguf)
* ModelScope: [OpenBMB/MiniCPM-V-2_6-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-2_6-gguf)

Download the language-model file (e.g. `ggml-model-Q4_0.gguf`) and the vision-model file `mmproj-model-f16.gguf`.

### MiniCPM-V 4.0 — official GGUF

* Hugging Face: [openbmb/MiniCPM-V-4-gguf](https://huggingface.co/openbmb/MiniCPM-V-4-gguf)
* ModelScope: [OpenBMB/MiniCPM-V-4-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-4-gguf)

Download the language-model file (e.g. `ggml-model-Q4_K_M.gguf`) and the vision-model file `mmproj-model-f16.gguf`.

### MiniCPM-V 4.6 — official GGUF

* Hugging Face: [openbmb/MiniCPM-V-4.6-gguf](https://huggingface.co/openbmb/MiniCPM-V-4.6-gguf)
* ModelScope: [OpenBMB/MiniCPM-V-4.6-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-4.6-gguf)

Download the language-model file (e.g. `MiniCPM-V-4_6-Q4_K_M.gguf`) and the vision-model file `mmproj-model-f16.gguf`.

---

## 5. Convert PyTorch → GGUF (optional)

For server-side conversion recipes in this Cookbook, see:

* [MiniCPM-V 4.6 GGUF](../../quantization/gguf/minicpm-v4_6_gguf_quantize.md)
* [MiniCPM-V 4.0 GGUF](https://github.com/OpenSQZ/MiniCPM-V-CookBook/blob/main/quantization/gguf/minicpm-v4_gguf_quantize.md)
* Additional versions: [Cookbook `quantization/gguf/`](https://github.com/OpenSQZ/MiniCPM-V-CookBook/tree/main/quantization/gguf)

Run conversion commands inside the **`llama.cpp`** subtree at the MiniCPM-V-Apps repo root.
