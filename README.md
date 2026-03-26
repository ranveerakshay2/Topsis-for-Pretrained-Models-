# Finding the Best Model for Text Sentence Similarity using TOPSIS

**Roll Number:** 102303453

---

## What's this about?

Ever wondered how to fairly compare a bunch of AI models when no single one wins on every metric? That's exactly the problem this assignment tackles.

I used **TOPSIS** — a multi-criteria decision-making method — to rank five popular pretrained sentence similarity models. The idea is simple: instead of just picking the model with the best accuracy, TOPSIS considers *all* the things that matter (accuracy, speed, size) and finds the one that's closest to a perfect solution while being furthest from the worst.

The task assigned to me (Roll No. ending in **3**) is **Text Sentence Similarity**.

---

## Models I Compared

I picked five well-known models from the sentence-transformers family — a mix of lightweight options, balanced choices, and heavy-hitters:

| Model | What it is |
|-------|-----------|
| `all-MiniLM-L6-v2` | A lean, fast transformer that punches above its weight on similarity tasks |
| `all-mpnet-base-v2` | The go-to general-purpose sentence embedding model — solid all-around |
| `paraphrase-MiniLM-L3-v2` | Extremely fast and tiny, built for paraphrase detection |
| `roberta-large-nli-stsb-mean-tokens` | The big gun — large RoBERTa fine-tuned specifically on STS tasks |
| `distilbert-base-nli-stsb-mean-tokens` | A distilled BERT that's been fine-tuned on STS-B — lighter than the full thing |

---

## What I Measured

Picking the "best" model means asking: best at *what*? I chose four criteria that capture both quality and practicality:

| Criterion | What it measures | Better when... |
|-----------|-----------------|----------------|
| **STS-B Score** | How well the model scores on the STS Benchmark | Higher |
| **Spearman Correlation** | How well its rankings match human similarity judgements | Higher |
| **Inference Time** | How many milliseconds it takes per sentence pair | Lower |
| **Model Size** | Number of parameters (in millions) | Lower |

The first two capture *how good* the model is. The last two capture *how usable* it is in practice.

---

## The Raw Numbers

Here's the decision matrix — the starting point for the TOPSIS calculation:

| Model | STS-B Score | Spearman ρ | Inference Time (ms) | Model Size (M params) |
|-------|-------------|------------|---------------------|-----------------------|
| all-MiniLM-L6-v2 | 88.5 | 0.814 | 14 | 22 |
| all-mpnet-base-v2 | 89.3 | 0.827 | 55 | 109 |
| paraphrase-MiniLM-L3-v2 | 84.2 | 0.771 | 6 | 17 |
| roberta-large-nli-stsb-mean-tokens | 90.1 | 0.831 | 120 | 355 |
| distilbert-base-nli-stsb-mean-tokens | 86.4 | 0.793 | 30 | 66 |

---

## How TOPSIS Works (step by step)

**Step 1 — Build the decision matrix**
Lay out all models and their scores across every criterion. That's the table above.

**Step 2 — Normalize it**
Since STS-B scores are in the 80s and inference times are in the tens, we can't compare them directly. We normalize each column using the Euclidean norm so everything is on the same scale:

$$r_{ij} = \frac{x_{ij}}{\sqrt{\sum_{i=1}^{m} x_{ij}^2}}$$

**Step 3 — Assign weights**
Not all criteria are equally important for sentence similarity. Accuracy matters most, but speed and size still count:
- STS-B Score → **0.35**
- Spearman Correlation → **0.30**
- Inference Time → **0.20**
- Model Size → **0.15**

**Step 4 — Compute the weighted matrix**
Multiply each normalized value by its weight.

**Step 5 — Find the ideal best and worst**
The *ideal best* (PIS) is a hypothetical model that scores maximum on every benefit criterion and minimum on every cost criterion. The *ideal worst* (NIS) is the opposite.

**Step 6 — Measure distances**
For each real model, calculate how far it is from the ideal best (D⁺) and from the ideal worst (D⁻) using Euclidean distance.

**Step 7 — Calculate the TOPSIS score**
The closeness coefficient tells us how close a model is to ideal, relative to how far it is from the worst:

$$C_i = \frac{D_i^-}{D_i^+ + D_i^-}$$

A score closer to **1** means closer to ideal. Closer to **0** means closer to the worst.

**Step 8 — Rank**
Sort by score, highest first.

---

## Results

| Rank | Model | TOPSIS Score |
|------|-------|-------------|
| 1 | **all-MiniLM-L6-v2** | **0.7712** |
| 2 | all-mpnet-base-v2 | 0.6248 |
| 3 | distilbert-base-nli-stsb-mean-tokens | 0.4691 |
| 4 | roberta-large-nli-stsb-mean-tokens | 0.2538 |
| 5 | paraphrase-MiniLM-L3-v2 | 0.1986 |

### Bar Chart
![TOPSIS Bar Chart](topsis_bar_chart.png)

### Radar Chart (Top 3 Models)
![TOPSIS Radar Chart](topsis_radar_chart.png)

---

## So... which model won and why?

**`all-MiniLM-L6-v2`** came out on top with a TOPSIS score of **0.7712** — and honestly, it makes a lot of sense when you think about it.

`roberta-large` technically has the best raw STS-B score (90.1), but it's also 355 million parameters and takes 120ms per inference. That's a huge trade-off for a marginal accuracy gain. TOPSIS correctly penalizes it for this.

On the other end, `paraphrase-MiniLM-L3-v2` is blazing fast but its accuracy (STS-B: 84.2) just doesn't hold up — so despite being tiny, it ranks last.

`all-MiniLM-L6-v2` sits in the sweet spot:
-  Strong accuracy — STS-B **88.5**, Spearman ρ **0.814**
-  Very fast — **14 ms** per pair, about 9× faster than roberta-large
-  Lightweight — just **22M parameters**, easy to deploy anywhere

It's the kind of model you'd actually want to ship to production.

---

## Files in this repo

```
Assignment-05/
├── Assignment_05_102303453.ipynb   # Full TOPSIS implementation in Python
├── topsis_bar_chart.png            # Bar chart of final TOPSIS scores
├── topsis_radar_chart.png          # Radar chart comparing top 3 models
└── README.md                       # You're reading it
```

---

## Running the notebook

No special setup needed. Just open `Assignment_05_102303453.ipynb` in Google Colab or Jupyter and run all cells top to bottom. The only dependencies are `numpy`, `pandas`, and `matplotlib` — all of which come pre-installed in Colab.

---

*Assignment 05 | Roll No. 102303453 | Text Sentence Similarity*
