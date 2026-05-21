# MiniCPM 4.1 - CPM.cu (on-device CUDA)

> [!NOTE]
> [CPM.cu](https://github.com/OpenBMB/CPM.cu) is OpenBMB's lightweight CUDA inference framework, purpose-built for the MiniCPM family. It pairs natively with the MiniCPM 4 / 4.1 EAGLE-FRSpec drafts and [InfLLM-V2](https://github.com/OpenBMB/infllmv2_cuda_impl) sparse-attention kernels to deliver end-device CUDA throughput close to optimised server stacks.

## 1. Install CPM.cu

```bash
git clone https://github.com/OpenBMB/CPM.cu.git
cd CPM.cu
git submodule update --init --recursive
pip install -e .
```

> [!TIP]
> CPM.cu compiles its own CUDA kernels — make sure `nvcc` matches your CUDA runtime. RTX 3060 / 4090 / Jetson Orin are routinely tested.

## 2. Install InfLLM-V2 kernels (for long context)

```bash
git clone https://github.com/OpenBMB/infllmv2_cuda_impl.git
cd infllmv2_cuda_impl
pip install -e .
```

## 3. Run with EAGLE-FRSpec speculative decoding

Use the OpenBMB drafts that are tuned for CPM.cu:

- [`openbmb/MiniCPM4.1-8B`](https://huggingface.co/openbmb/MiniCPM4.1-8B) — base target
- [`openbmb/MiniCPM4.1-8B-Eagle3`](https://huggingface.co/openbmb/MiniCPM4.1-8B-Eagle3) — EAGLE3 draft

```python
from cpm_cu import CPMRunner

runner = CPMRunner(
    target_model="openbmb/MiniCPM4.1-8B",
    draft_model="openbmb/MiniCPM4.1-8B-Eagle3",
    spec_method="eagle3",
    num_speculative_tokens=5,
    dtype="bf16",
    max_context_len=131072,
    use_infllm_v2=True,    # enable sparse attention for long context
)

reply = runner.chat(
    [{"role": "user", "content": "Write a short article about edge AI."}],
    max_new_tokens=512,
    temperature=0.7,
    top_p=0.8,
)
print(reply)
```

> The `CPMRunner` API surface is illustrative — refer to the [CPM.cu README](https://github.com/OpenBMB/CPM.cu#readme) for the exact entry point in your installed version.

## 4. Run with MiniCPM 4

For MiniCPM 4, swap the targets to the CPM.cu-aligned drafts:

- [`openbmb/MiniCPM4-8B`](https://huggingface.co/openbmb/MiniCPM4-8B) — base target
- [`openbmb/MiniCPM4-8B-Eagle-FRSpec`](https://huggingface.co/openbmb/MiniCPM4-8B-Eagle-FRSpec) — FRSpec draft
- [`openbmb/MiniCPM4-8B-Eagle-FRSpec-QAT`](https://huggingface.co/openbmb/MiniCPM4-8B-Eagle-FRSpec-QAT) — quantization-aware variant

## 5. Notes

- CPM.cu is **first-party OpenBMB code** but currently has lighter release cadence than vLLM / SGLang. Pin to a known-good commit for reproducible deployments.
- For production GPU servers, vLLM with the EAGLE3 draft (see [vLLM guide](vllm.html)) is usually easier to operate.
- CPM.cu plus InfLLM-V2 is the only stack we recommend for **end-device** long-context (128K+) on consumer GPUs.
