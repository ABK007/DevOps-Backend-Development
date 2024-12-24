# GPU Based Cloud Hosting Services

GPUs for LLM inference and model training are too expensive to buy.

For that, we can use GPU cloud services where we can rent out GPUs of various compute resources
based on our requirements.

These services usually charge on per hour basis of GPU usage

Here is the list of some of these services:

1. [Runpod.io](https://www.runpod.io/)

Here is the list of GPU available and their rates:
| **GPU Model**                | **VRAM** | **vCPUs** | **RAM** | **Price (per hour)** |
|------------------------------|----------|-----------|---------|-----------------------|
| Nvidia A30                   | 24GB     | 8         | 31GB    | $0.22                 |
| Nvidia RTX A4000             | 16GB     | 4         | 20GB    | $0.32                 |
| Nvidia A4500                 | 20GB     | 4         | 29GB    | $0.34                 |
| Nvidia A5000                 | 24GB     | 8         | 24GB    | $0.36                 |
| Nvidia A40                   | 48GB     | 9         | 50GB    | $0.39                 |
| Nvidia RTX 4090              | 24GB     | 6         | 27GB    | $0.69                 |
| Nvidia A6000                 | 48GB     | 8         | 62GB    | $0.76                 |
| Nvidia A6000 Ada             | 48GB     | 14        | 58GB    | $0.88                 |
| Nvidia L4                    | 24GB     | 12        | 50GB    | $0.43                 |
| Nvidia RTX A4000 Ada         | 20GB     | 4         | 16GB    | $0.46                 |
| Nvidia RTX A5000             | 24GB     | 6         | 24GB    | $0.49                 |
| Nvidia V100                  | 16GB     | 4         | 16GB    | $0.54                 |
| Nvidia RTX A6000             | 48GB     | 6         | 24GB    | $0.59                 |
| Nvidia L40S                  | 48GB     | 12        | 48GB    | $1.75                 |

This table provides a complete list of GPUs currently available on RunPod, including their specifications and pricing as of January 2024.

Citations:
[1] https://getdeploying.com/reference/cloud-gpu
[2] https://docs.runpod.io/hosting/partner-requirements
[3] https://www.runpod.io/gpu/4090
[4] https://www.runpod.io/pricing


2. [Bento.ml](https://www.bentoml.com/)
Bento ml is another company providing cloud GPU services.

Here is a table summarizing the GPU and compute resource pricing based on the provided data:

| **Resource Type**            | **GPU/CPU Model** | **Specifications**                        | **Price ($/sec)** | **Price ($/hr)** |
|------------------------------|-------------------|------------------------------------------|-------------------|-------------------|
| **GPU**                      | gpu.t4.1          | 1 T4, 16GB VRAM, 4 vCPU, 13 GB RAM     | $0.5111           | $1.84             |
| **GPU**                      | gpu.t4.2          | 2 T4, 32GB VRAM, 16 vCPU, 60 GB RAM    | $1.4062           | $5.06             |
| **GPU**                      | gpu.t4.4          | 4 T4, 64GB VRAM, 32 vCPU, 120 GB RAM   | $2.5103           | $9.03             |
| **GPU**                      | gpu.l4.1          | 1 L4, 24GB VRAM, 8 vCPU, 32 GB RAM     | $1.0143           | $3.66             |
| **GPU**                      | gpu.l4.2          | 2 L4, 48GB VRAM, 24 vCPU, 96 GB RAM    | $2.3797           | $8.56             |
| **GPU**                      | gpu.l4.4          | 4 L4, 96GB VRAM, 48 vCPU, 192 GB RAM   | $4.6807           | $16.83            |
| **GPU**                      | gpu.a100.1        | 1 A100, 40GB VRAM, 12 vCPU, 85 GB RAM  | $3.7419           | $13.48            |
| **GPU**                      | gpu.a100.2        | 2 A100, 80GB VRAM, 24 vCPU, 170 GB RAM | $7.4153           | $26.69            |
| **GPU**                      | gpu.a100.4        | 4 A100, 160GB VRAM, 48 vCPU, 340 GB RAM| $14.7620          | $53.44            |
| **CPU**                      | cpu.1             | 1 vCPU, 4 GB RAM                         | $0.0476           | $0.17136          |
| **CPU**                      | cpu.2             | 2 vCPU, 8 GB RAM                         | $0.0986           | $0.35496          |
| **CPU**                      | cpu.4             | 4 vCPU, 16 GB RAM                        | $0.1972           | $0.71064          |
| **CPU**                      | cpu.8             | 8 vCPU, 32 GB RAM                        | $0.4079           | $1.46784          |
| **CPU**                      | cpu.16            | 16 vCPU, 64 GB RAM                       | $0.8012           | $2.88544          |

This table provides a comprehensive overview of the GPU and CPU pricing along with their specifications based on the data provided in your query.



3. [huggingface.co](https://huggingface.co/pricing)
   Here is the table summarizing the GPU and compute resources pricing from Hugging Face:

| **Resource Type**            | **Provider** | **Architecture** | **GPUs** | **GPU Memory** | **Hourly Rate ($)** |
|------------------------------|--------------|------------------|----------|----------------|----------------------|
| **GPU**                      | AWS          | NVIDIA T4       | 1        | 14GB           | $0.50                |
| **GPU**                      | AWS          | NVIDIA T4       | 4        | 56GB           | $3.00                |
| **GPU**                      | AWS          | NVIDIA L4       | 1        | 24GB           | $0.80                |
| **GPU**                      | AWS          | NVIDIA L4       | 4        | 96GB           | $3.80                |
| **GPU**                      | AWS          | NVIDIA L40S     | 1        | 48GB           | $1.80                |
| **GPU**                      | AWS          | NVIDIA L40S     | 4        | 192GB          | $8.30                |
| **GPU**                      | AWS          | NVIDIA L40S     | 8        | 384GB          | $23.50               |
| **GPU**                      | AWS          | NVIDIA A10G     | 1        | 24GB           | $1.00                |
| **GPU**                      | AWS          | NVIDIA A10G     | 4        | 96GB           | $5.00                |
| **GPU**                      | AWS          | NVIDIA A100     | 1        | 80GB           | $4.00                |
| **GPU**                      | AWS          | NVIDIA A100     | 2        | 160GB          | $8.00                |
| **GPU**                      | AWS          | NVIDIA A100     | 4        | 320GB          | $16.00               |
| **GPU**                      | AWS          | NVIDIA A100     | 8        | 640GB          | $32.00               |
| **GPU**                      | GCP          | NVIDIA T4       | 1        | 16GB           | $0.50                |
| **GPU**                      | GCP          | NVIDIA L4       | 1        | 24GB           | $0.70                |
| **GPU**                      | GCP          | NVIDIA A100     | 1        | 80GB           | $3.60                |
| **GPU**                      | GCP          | NVIDIA A100     | 2        | 160GB          | $7.20                |
| **GPU**                      | GCP          | NVIDIA A100     | 4        | 320GB          | $14.40               |
| **GPU**                      | GCP          | NVIDIA A100     | 8        | 640GB          | $28.80               |
| **GPU**                      | GCP          | NVIDIA H100     | 1        | 80GB           | $10.00               |
| **GPU**                      |- GCP         |- NVIDIA H100    |-2       |-160 GB         |-20.00                |

This table provides an overview of the GPU pricing and specifications available on Hugging Face as of now.

Citations:
[1] https://huggingface.co/pricing


4. [replicate.com](https://replicate.com/pricing)
Here is the GPU pricing table based on the data from Replicate:

| **GPU Model**                       | **Price ($/sec)** | **Price ($/hr)** | **GPU RAM** | **Total RAM** |
|-------------------------------------|--------------------|-------------------|--------------|---------------|
| Nvidia A100 (80GB)                 | $0.001400          | $5.04             | 80GB         | 144GB         |
| 2x Nvidia A100 (80GB)              | $0.002800          | $10.08            | 160GB        | 288GB         |
| 4x Nvidia A100 (80GB)              | $0.005600          | $20.16            | 320GB        | 576GB         |
| 8x Nvidia A100 (80GB)              | $0.011200          | $40.32            | 640GB        | 960GB         |
| Nvidia L40S                         | $0.000975          | $3.51             | 48GB         | 65GB          |
| 2x Nvidia L40S                      | $0.001950          | $7.02             | 96GB         | 144GB         |
| 4x Nvidia L40S                      | $0.003900          | $14.04            | 192GB        | 288GB         |
| 8x Nvidia L40S                      | $0.007800          | $28.08            | 384GB        | 576GB         |
| Nvidia T4                           | $0.000225          | $0.81             | 16GB         | 16GB          |
| Nvidia H100                        | $0.001525          | $5.49             | -            | -             |
| 2x Nvidia H100                      | $0.003050          | $10.98            | -            | -             |
| 4x Nvidia H100                      | $0.006100          | $21.96            | -            | -             |
| 8x Nvidia H100                      | $0.012200          | $43.92            | -            | -             |

This table summarizes the GPU pricing available on Replicate, including specifications for GPU RAM and total RAM where applicable.

Citations:
[1] https://replicate.com/pricing