---
license: apache-2.0
language:
- en
- zh
base_model:
- BytedanceDouyinContent/SAIL-VL-2B
pipeline_tag: image-text-to-text
tags:
- gguf
- vision-language
- multimodal
- llama.cpp
- qwen2
- internvit
---

# SAIL-VL-2B GGUF

**Hugging Face release:** [ShayonSarker/SAIL-VL-2B-GGUF](https://huggingface.co/ShayonSarker/SAIL-VL-2B-GGUF)

Unofficial [GGUF](https://github.com/ggml-org/ggml) conversion of [BytedanceDouyinContent/SAIL-VL-2B](https://huggingface.co/BytedanceDouyinContent/SAIL-VL-2B), packaged for inference with `llama.cpp`.

The repository contains the Qwen2 text model in F16, Q8_0, and Q4_K_M formats, plus the InternViT multimodal projector required for image prompts.

This GitHub repository contains the reproducible Kaggle conversion pipeline and release metadata. The multi-gigabyte GGUF artifacts are hosted on Hugging Face at [ShayonSarker/SAIL-VL-2B-GGUF](https://huggingface.co/ShayonSarker/SAIL-VL-2B-GGUF).

## Repository contents

| File | Purpose |
|---|---|
| [`SAIL-VL-2B-GGUF.ipynb`](SAIL-VL-2B-GGUF.ipynb) | End-to-end CPU/Kaggle conversion, validation, and upload notebook |
| [`REPRO.json`](REPRO.json) | Conversion commit, inference output, sizes, and short hashes |
| [`SHA256.txt`](SHA256.txt) | Full checksums for the published GGUF artifacts |
| [`LICENSE`](LICENSE) | Apache License 2.0 |

## Published artifacts

Download the binaries from the [Hugging Face release](https://huggingface.co/ShayonSarker/SAIL-VL-2B-GGUF/tree/main):

- [`sail-vl-2b-f16.gguf`](https://huggingface.co/ShayonSarker/SAIL-VL-2B-GGUF/resolve/main/sail-vl-2b-f16.gguf)
- [`sail-vl-2b-Q8_0.gguf`](https://huggingface.co/ShayonSarker/SAIL-VL-2B-GGUF/resolve/main/sail-vl-2b-Q8_0.gguf)
- [`sail-vl-2b-Q4_K_M.gguf`](https://huggingface.co/ShayonSarker/SAIL-VL-2B-GGUF/resolve/main/sail-vl-2b-Q4_K_M.gguf)
- [`mmproj-sail-vl-2b-f16.gguf`](https://huggingface.co/ShayonSarker/SAIL-VL-2B-GGUF/resolve/main/mmproj-sail-vl-2b-f16.gguf)

## Files

| File | Approx. size | Purpose |
|---|---:|---|
| `sail-vl-2b-f16.gguf` | 3560.4 MB | Reference text model |
| `sail-vl-2b-Q8_0.gguf` | 1894.5 MB | Balanced text quantization |
| `sail-vl-2b-Q4_K_M.gguf` | 1117.3 MB | Smaller text quantization |
| `mmproj-sail-vl-2b-f16.gguf` | 628.2 MB | Vision projector for image input |

Use the `mmproj` file with any text GGUF from this repository. The validation run below used `sail-vl-2b-Q8_0.gguf`.

## Rebuild the conversion

Open [`SAIL-VL-2B-GGUF.ipynb`](SAIL-VL-2B-GGUF.ipynb) in Kaggle, enable Internet, select the CPU accelerator, add `HF_TOKEN` as a Kaggle secret, and run all cells. The notebook downloads the source model, builds `llama.cpp`, converts the text and vision projector, runs the semantic gate, writes checksums, and uploads the release to Hugging Face.

Generated GGUFs and local build directories are intentionally ignored by Git; download them from the Hugging Face release instead.

## Quick start

Download the files with the Hugging Face Hub:

```bash
python -c "from huggingface_hub import snapshot_download; snapshot_download('ShayonSarker/SAIL-VL-2B-GGUF', local_dir='SAIL-VL-2B-GGUF')"
```

Build a current `llama.cpp` checkout with `llama-mtmd-cli`:

```bash
git clone https://github.com/ggerganov/llama.cpp.git
cd llama.cpp
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release --target llama-mtmd-cli llama-cli
```

Run an image prompt:

```bash
./llama.cpp/build/bin/llama-mtmd-cli \
  -m ./SAIL-VL-2B-GGUF/sail-vl-2b-Q4_K_M.gguf \
  --mmproj ./SAIL-VL-2B-GGUF/mmproj-sail-vl-2b-f16.gguf \
  --image ./image.jpg \
  -p "Describe the image and read any visible text." \
  --temp 0 -n 256
```

For text-only prompts, use `llama-cli` with one of the text GGUFs; the `mmproj` file is not needed.

## Validation

The conversion was tested with `llama.cpp` commit `fc343a84bbd925b37dde3219de35ea0bed50d630` on CPU. All GGUF files passed the GGUF magic check, and the Q8_0 model passed a semantic image test using the source repository's `statics/14.jpg`:

> The image features a French Bulldog lying on a worn, turquoise wooden deck. ... The text overlaid on the image reads "MONDAY. JUST...MONDAY."

The exact inference output, file sizes, conversion commit, and short hashes are recorded in [`REPRO.json`](REPRO.json). Full SHA-256 checksums are in [`SHA256.txt`](SHA256.txt).

The current build may print a non-fatal tokenizer metadata warning about token `128247` (`</s>`). It did not prevent inference or the semantic validation gate.

## License and attribution

The source model is distributed under the Apache License 2.0. See [`LICENSE`](LICENSE) and the [original model card](https://huggingface.co/BytedanceDouyinContent/SAIL-VL-2B) for upstream details, training information, and citation.

This repository is a community conversion and is not an official ByteDance release.
