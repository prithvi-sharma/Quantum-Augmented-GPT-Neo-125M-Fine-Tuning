# Quantum‑Augmented Fine‑Tuning of GPT‑Neo‑125M
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE) 

An experimental repo exploring hybrid quantum‑classical fine‑tuning of a tiny LLM. Basically, augmented EleutherAI/gpt-neo-125M with a tiny VQC adapter and compared classical LoRA and partial‑fine‑tuning baselines using wikitext‑2's data.

---

## Overview
- Model: EleutherAI/gpt-neo-125M
- Dataset: wikitext-2-raw-v1 (small subset, max_length=64)
- Experiments:
  - Baseline
  - VQC-only Baseline
  - LoRA-only Baseline
  - Combined LoRA and VQC Model - 3 Qubits, Original Circuit, VQC LR 5e-4
  - Combined LoRA and VQC Model - 4 Qubits, Original Circuit, VQC LR 5e-4
  - Combined LoRA and VQC Model - 4 Qubits, Modified Circuit, VQC LR 5e-4
  - Combined LoRA and VQC Model - 4 Qubits, Modified Circuit, VQC LR 1e-3
  - Combined LoRA and VQC Model - 4 Qubits, Modified Circuit, VQC LR 1e-4
- Evaluation:
 - Validation loss printed every 100 steps.
 - Final numbers reported after 2000 training steps for quick comparison.
 - Perplexity computed as exp(val_loss) for interpretability.

---

## Hardware (Google Colab)
- CPU : Dual Core x86_64
- RAM : ~ 12.5 GB
- CPU : Tesla T4
- VRAM : 16 GB

---

## Results 

| Method                                                                                     | Loss   | Perplexity | Improvement vs Baseline (%) | Improvement vs LoRA (%) |
|--------------------------------------------------------------------------------------------|-------:|-----------:|---------------------------:|------------------------:|
| Baseline                                                                                   | 1.8562 | 6.3996    | 0.00%                      | -2.07%                 |
| LoRA‑only Baseline                                                                         | 1.8351 | 6.2658    | 2.06%                      | 0.00%                  |
| VQC‑only (3 Qubits, Original Circuit)                                                      | 2.0492 | 7.7600    | -21.27%                    | -23.94%                |
| LoRA + VQC (3 Qubits, Original Circuit, VQC LR=5e‑4)                                       | 1.8352 | 6.2661    | 2.05%                      | -0.01%                 |
| LoRA + VQC (4 Qubits, Original Circuit, VQC LR=5e‑4)                                       | 1.8133 | 6.1349    | 4.14%                      | 2.10%                  |
| LoRA + VQC (4 Qubits, Modified Circuit, VQC LR=5e‑4)                                       | 1.7637 | 5.8428    | 8.69%                      | 6.75%                  |
| LoRA + VQC (4 Qubits, Modified Circuit, VQC LR=1e‑3)                                       | 1.8030 | 6.0666    | 5.20%                      | 3.20%                  |
| LoRA + VQC (4 Qubits, Modified Circuit, VQC LR=1e‑4)                                       | 1.8366 | 6.2800    | 1.86%                      | -0.29%                 |


- Best model: LoRA + VQC (4 qubits, Modified Circuit, VQC LR=5e-4)
  - Perplexity : 5.8428
  - 8.69% improvement vs baseline
  - 6.75% improvement vs LoRA

However, the findings are a bit deeper.....

### Loss reduction  graph

- When VQC modules are added (VQC-only and VQC + LoRA), training shows a large initial loss spike then slowly recovers to similar final loss as baseline.

<img src="/Output/Graph/whole_steps_graph.JPG" width="100%">

- Zooming into the 500–2K step range reveals where real improvement occurs. After the initial adjustment, the VQC parameters begin producing useful corrections and the combined model starts to match or beat the baseline.

<img src="/Output/Graph/500to2K_steps_graph.JPG" width="100%">

#### Key Observations: 
  1. 1200 Steps : VQC+LoRA (best variant) begins to improve over just LoRA and shows better loss
  2. 1300 Steps : VQC+LoRA starts improve over baseline
  3. 1450 Steps : LoRA starts improve over baseline

- So regardless of the initial loss boost, VQC + LoRA not only performs better at end, it also improves on Baseline before LoRA does (150 steps)!
  
<strong> My Thinking </strong> : Randomly initialized Q layers produce mismatched. Just massive corrections to pretrained hidden states and as their parameters are slowly adjusted, these corrections weaken and the model converges. During this convergence, it gets better then LoRA ending with close to 6.8% improvement and over baseline about 8.7%

Quite impressive!

---

## TL,DR

- Seeing if adding Quantum to LoRA for training helps?
- It looked bad at the start, but in the long run outperforms by 7%.

Just trying to make small LLM models better for specific tasks and thus more feasible for low‑resource consuming end-devices.
Let me know what you think — if you wanna discuss about this, always keen : <mail@prithvisharma.com>

Thanks for reading. See ya o/

(Detailed pre-print under review..... coming soon)
