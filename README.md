Convert models for llama.cpp

1. Install llama.cpp (if not already installed)

```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make -j
```

(Optional but recommended for GPU support: Install CUDA and compile with make LLAMA_CUDA=1 -j)

2. Download the Original Model
You need the original model in one of these formats:

PyTorch (*.pth or *.bin + config.json)

Hugging Face Transformers (saved in safetensors or bin)

GGML (older llama.cpp format, .bin)

Example (for Hugging Face models):

```bash
git lfs install
git clone https://huggingface.co/Meta-Llama-3-8B-Instruct ./models/Meta-Llama-3-8B
```

Replace with your desired model.)

3. Convert to GGUF
(A) From PyTorch/Hugging Face → GGUF
```bash
# Install Python dependencies
pip install torch numpy sentencepiece


# Convert to FP16 GGUF (recommended for most GPUs)
python3 llama.cpp/convert.py ./models/Meta-Llama-3-8B --outtype f16 --outfile ./models/llama-3-8b.gguf
```
(B) From Older GGML → GGUF

```bash
./llama.cpp/quantize ./models/old-ggml-model.bin ./models/new-model.gguf q4_0
```

4. (Optional) Quantize for Smaller Size & Faster Inference

```bash
./llama.cpp/quantize ./models/llama-3-8b.gguf ./models/llama-3-8b-Q4_K_M.gguf q4_k_m
```

Common Quantization Options:

Type	RAM Usage	Quality	Speed
q8_0	High	Best	Fast
q5_k_m	Medium	Great	Good
q4_k_m	Low	Good	Fast
q2_k	Very Low	Basic	Fastest
5. Verify the Model

```bash
./llama.cpp/main -m ./models/llama-3-8b-Q4_K_M.gguf -p "Hello, how are you?"
```


Recommended Settings for CPU vs. GPU
Hardware	Best Format	Performance	Quality
CPU	q4_k_m (4-bit quantized)	⚡ Very Fast	Good
CPU	q5_k_m (5-bit quantized)	Fast	Great
CPU	FP32 (full precision)	Slow	Best
GPU	FP16 (half precision)	⚡⚡ Fastest	Near-FP32
GPU	q4_k_m (4-bit quantized)	⚡⚡⚡ Fastest	Good

Better Alternatives for CPU:

```bash
# Option A: FP32 (full precision, best quality but slow)
python3 convert.py ./models/llama-3-8b --outtype f32 --outfile ./models/llama-3-8b.f32.gguf

# Option B: Quantized 4-bit (best speed/quality tradeoff)
./quantize ./models/llama-3-8b.f16.gguf ./models/llama-3-8b.q4_k_m.gguf q4_k_m
```

```bash
python3 convert_hf_to_gguf.py ../../../models/Qwen/Qwen3-0.6B --outtype f32 --outfile ../../../models/Qwen3-0.6B.f32.gguf
```