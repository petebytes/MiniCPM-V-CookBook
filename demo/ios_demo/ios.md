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

Open **`MiniCPM-V-demo/MiniCPM-V-demo.xcodeproj`** in Xcode, pick a destination device, then tap **Run**.

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

Upstream currently targets **MiniCPM-V 2.6**, **4.0**, and **4.6**. Download matching **language-model** GGUF (e.g. Q4\_K\_M) plus **`mmproj-model-f16.gguf`** for the projector / vision stack. Typical total size and RAM guidance are summarized in the upstream README (hardware section).

### MiniCPM-V 2.6 — official GGUF

* Hugging Face: [openbmb/MiniCPM-V-2_6-gguf](https://huggingface.co/openbmb/MiniCPM-V-2_6-gguf)
* ModelScope: [OpenBMB/MiniCPM-V-2_6-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-2_6-gguf)

### MiniCPM-V 4.0 — official GGUF

* Hugging Face: [openbmb/MiniCPM-V-4-gguf](https://huggingface.co/openbmb/MiniCPM-V-4-gguf)
* ModelScope: [OpenBMB/MiniCPM-V-4-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-4-gguf)

### MiniCPM-V 4.6 — official GGUF

* Hugging Face: [openbmb/MiniCPM-V-4.6-gguf](https://huggingface.co/openbmb/MiniCPM-V-4.6-gguf)
* ModelScope: [OpenBMB/MiniCPM-V-4.6-gguf](https://modelscope.cn/models/OpenBMB/MiniCPM-V-4.6-gguf)

*(Example filenames for 4.6 may appear as `MiniCPM-V-4_6-Q4_K_M.gguf`; always follow filenames listed in each HF / ModelScope repo.)*

---

## 5. Convert PyTorch → GGUF (optional)

For server-side conversion recipes in this Cookbook, see:

* [MiniCPM-V 4.6 GGUF](../../quantization/gguf/minicpm-v4_6_gguf_quantize.md)
* [MiniCPM-V 4.0 GGUF](https://github.com/OpenSQZ/MiniCPM-V-CookBook/blob/main/quantization/gguf/minicpm-v4_gguf_quantize.md)
* Additional versions: [Cookbook `quantization/gguf/`](https://github.com/OpenSQZ/MiniCPM-V-CookBook/tree/main/quantization/gguf)

Run conversion commands inside the **`llama.cpp`** subtree at the MiniCPM-V-Apps repo root.
