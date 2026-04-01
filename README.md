# confidential-grok2

Tinfoil config repo for running `xai-org/grok-2` with SGLang in an 8-GPU confidential VM.

## What Is Wired In

- 8 GPU / 32 CPU / 512 GB sizing, matching the other large multi-GPU model repos here.
- SGLang launch via `python3 -m sglang.launch_server`.
- Grok-2-specific server flags from the xAI model card: `--model-path`, `--tokenizer-path`, `--tp 8`, `--quantization fp8`, and `--attention-backend triton`.
- H200-oriented memory settings: `--mem-fraction-static 0.9`, `--context-length 32768`, and `--chunked-prefill-size 4096`.
- Tinfoil shim auth on the inference endpoints, with `/health` and `/metrics` still exposed for ops.

## Current Pins

- Target hardware: `8x H200`, `CUDA 13.0`
- Hugging Face model revision: `xai-org/grok-2@daf4395a80ad177386cfe39641b64fc12b1d70ed`
- MPK payload filename received: `daf4395a80ad177386cfe39641b64fc12b1d70ed.mpk`
- Modelpack ID: `695363c502c9cbaa2a111caf4cb28ba76e80c57d159fb364524ad19ffdbdc65a_539040456704_19c36cba-d4d0-5be1-ab22-1a10dc7313e9`
- SGLang image: `lmsysorg/sglang:nightly-dev-cu13-20260331-b4f79c0d@sha256:bf003fc04a517e49f05919ab136dab1fca58e46b11190097d31eb5d324a3a4ea`

## Notes

- The model is loaded from `/tinfoil/mpk/mpk-695363c502c9cbaa2a111caf4cb28ba76e80c57d159fb364524ad19ffdbdc65a`.
- `--tokenizer-path` points at `tokenizer.tok.json` inside that same mount.
- `--mem-fraction-static` is set to `0.9` for H200 instead of the earlier conservative `0.8`.
- This config does not set SGLang `--api-key`; Tinfoil shim auth should own external access control.
- If startup fails with a GPU peer-access error, add `--enable-p2p-check` to the container command.
