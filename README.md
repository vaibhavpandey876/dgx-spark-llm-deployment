# 🚀 DGX Spark On-Premise LLM Deployment

> 2x NVIDIA DGX Spark | Qwen3.6-35B-A3B-NVFP4
> vLLM Serving | Full RAG Stack | CUDA 13.0
> 240GB Combined GPU Memory

## 🏗️ Infrastructure
┌─────────────────────────────────────────┐
│         oso-spark-001 (Primary)         │
│         NVIDIA DGX Spark                │
│         120GB GPU Memory                │
│                                         │
│  ┌─────────────┐  ┌──────────────────┐  │
│  │ embed-service│  │ reranker-service │  │
│  │ Port: 8001  │  │ Port: 8100       │  │
│  └─────────────┘  └──────────────────┘  │
│                                         │
│  ┌───────────────────────────────────┐  │
│  │ vLLM Engine                       │  │
│  │ Qwen3.6-35B-A3B-NVFP4            │  │
│  │ 51GB VRAM | 8 concurrent reqs    │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│         oso-spark-002 (Secondary)       │
│         NVIDIA DGX Spark                │
│         120GB GPU Memory                │
│         Same Stack — Load Distribution  │
└─────────────────────────────────────────┘
## ⚙️ Tech Stack

| Component | Technology |
|---|---|
| Hardware | NVIDIA DGX Spark x2 |
| GPU | NVIDIA GB10 |
| CUDA | 13.0 |
| Driver | 580.159.03 |
| LLM Engine | vLLM |
| Model | nvidia/Qwen3.6-35B-A3B-NVFP4 |
| Embedding | embed-service (uvicorn) |
| Reranking | reranker-service (uvicorn) |
| Serving | uvicorn + vLLM |

## 🧠 Model Details

| Property | Value |
|---|---|
| Model | Qwen3.6-35B-A3B-NVFP4 |
| Parameters | 35 Billion |
| Quantization | NVFP4 (NVIDIA optimized) |
| VRAM Usage | ~51GB per box |
| GPU Utilization | 45% (--gpu-memory-utilization 0.45) |
| Max Concurrent | 8 sequences |
| Context Length | 204,800 tokens |
| Reasoning | Qwen3 reasoning parser enabled |
| Tool Calling | Enabled (qwen3_xml parser) |

## 🔧 vLLM Launch Config

```bash
vllm serve nvidia/Qwen3.6-35B-A3B-NVFP4 \
  --reasoning-parser qwen3 \
  --enable-auto-tool-choice \
  --tool-call-parser qwen3_xml \
  --gpu-memory-utilization 0.45 \
  --max-num-seqs 8 \
  --no-async-scheduling \
  --max-model-len 204800
