# Zeroshot consistency statistics (20 contracts)

- Source: `zeroshot_error_clusters_final_20_corrected_scored.json`
- Unit of agreement: **semantic clusters** after Qwen clustering + DeepSeek correction.
- Two findings are treated as the same defect if they fall in the **same final cluster**.
- Contracts: **20**; total clusters: **914**; total findings (occurrences): **2972**.

### Definitions

| Term | Definition |
|------|------------|
| Within-model multi-run finding | A finding whose cluster also contains ≥1 finding from the **same model, different run** |
| Within-model all-3-run finding | A finding whose cluster contains the **same model in run1, run2, and run3** |
| Cross-model finding | A finding whose cluster contains ≥**2 distinct models** |
| All-3-model finding | A finding whose cluster contains **all 3 models** |
| Pairwise Jaccard (runs) | For one model on one contract, let C_r = set of cluster IDs hit by run r. Jaccard(run i, run j) = \|C_i ∩ C_j\| / \|C_i ∪ C_j\|, then average over contracts. |
| Pairwise Jaccard (models) | Same idea using each model’s set of cluster IDs on a contract, then average over contracts. |

## 1. Output volume by model × run

| Model | run1 | run2 | run3 | Total (3 runs) |
|-------|-----:|-----:|-----:|---------------:|
| Fable 5 | 348 | 335 | 341 | 1024 |
| GPT-5.6 Sol | 565 | 561 | 541 | 1667 |
| Gemini 3.8 Flash | 94 | 90 | 97 | 281 |
| **All** | 1007 | 986 | 979 | **2972** |

## 2. Within-model consistency across 3 runs

Share of each model’s own outputs that reappear (semantically) in another run of the **same** model.

| Model | Findings | Multi-run consistent | Share of model output | All-3-run consistent | Share of model output | Mean pairwise Jaccard (runs) | Mean triple Jaccard |
|-------|----------:|---------------------:|----------------------:|---------------------:|----------------------:|-----------------------------:|--------------------:|
| Fable 5 | 1024 | 898 | 87.7% | 731 | 71.4% | 65.4% | 53.2% |
| GPT-5.6 Sol | 1667 | 1379 | 82.7% | 1033 | 62.0% | 59.8% | 47.3% |
| Gemini 3.8 Flash | 281 | 239 | 85.1% | 180 | 64.1% | 64.3% | 52.5% |
| **All models** | 2972 | 2516 | 84.7% | 1944 | 65.4% | 63.2% | 51.0% |

### 2.1 Pairwise run Jaccard by model (averaged over contracts)

| Model | run1↔run2 | run1↔run3 | run2↔run3 |
|-------|----------:|----------:|----------:|
| Fable 5 | 64.4% | 66.9% | 64.9% |
| GPT-5.6 Sol | 59.0% | 60.2% | 60.1% |
| Gemini 3.8 Flash | 64.8% | 60.8% | 67.4% |

## 3. Cross-model consistency

Share of findings that land in a cluster supported by **more than one model**.

| Scope | Findings | In multi-model clusters | Share of output | In all-3-model clusters | Share of output |
|-------|----------:|------------------------:|----------------:|------------------------:|----------------:|
| All findings | 2972 | 1949 | 65.6% | 859 | 28.9% |
| Fable 5 only | 1024 | 802 | 78.3% | 305 | 29.8% |
| GPT-5.6 Sol only | 1667 | 882 | 52.9% | 320 | 19.2% |
| Gemini 3.8 Flash only | 281 | 265 | 94.3% | 234 | 83.3% |

### 3.1 Pairwise model Jaccard (averaged over contracts)

| Model pair | Mean Jaccard |
|------------|-------------:|
| Fable 5 ↔ GPT-5.6 Sol | 34.7% |
| Fable 5 ↔ Gemini 3.8 Flash | 23.8% |
| GPT-5.6 Sol ↔ Gemini 3.8 Flash | 23.8% |


## 3b. Missed by one model (found by the other two)

Defects that **both of the other models** reported, but **this model did not**.

- Unit: a final semantic cluster whose `models` set includes the other two models and excludes this one.
- **Missed clusters**: how many such clusters (also shown as % of all 914 clusters).
- **When both others found it**: among clusters that already contain both other models, the share where this model is missing (miss rate conditional on the other two agreeing).
- **Findings in missed clusters**: total occurrences sitting in those clusters, as a share of all 2972 findings (weights larger clusters more heavily).

| Model | Missed clusters | / all clusters | When both others found it | Findings in missed clusters | / all findings |
|-------|----------------:|---------------:|--------------------------:|----------------------------:|---------------:|
| Fable 5 | 8 | 0.9% | 7.4% (8/108) | 29 | 1.0% |
| GPT-5.6 Sol | 9 | 1.0% | 8.3% (9/109) | 42 | 1.4% |
| Gemini 3.8 Flash | 208 | 22.8% | 67.5% (208/308) | 1019 | 34.3% |

Note: Gemini’s high miss rate is expected given its much smaller output volume; Fable and GPT rarely miss a defect that the other two both found.


## 3c. Coverage of the 3-model defect union

Define the **union** as all final semantic clusters on the 20 contracts (**914** clusters) — every distinct defect found by at least one model.

For each model, **union coverage** = (# clusters containing that model) / (# clusters in the union).

Because a cluster can include multiple models, these percentages **overlap** and need not sum to 100%.  
For comparison, each model’s share of raw finding volume (sums to 100%) is also shown.

| Model | Clusters in union | Share of defect union | Findings | Share of all findings | Clusters only this model |
|-------|------------------:|----------------------:|---------:|----------------------:|-------------------------:|
| Fable 5 | 441 | 48.2% | 1024 | 34.5% | 124 (13.6% of union) |
| GPT-5.6 Sol | 768 | 84.0% | 1667 | 56.1% | 452 (49.5% of union) |
| Gemini 3.8 Flash | 130 | 14.2% | 281 | 9.5% | 13 (1.4% of union) |
| **Union (any model)** | **914** | 100% | **2972** | 100% | — |

## 4. Cluster-level agreement summary

| Cluster type | Clusters | Share of clusters | Findings in these clusters | Share of all findings |
|--------------|---------:|------------------:|---------------------------:|----------------------:|
| Singleton (1 finding) | 310 | 33.9% | 310 | 10.4% |
| Multi-finding, single-model | 279 | 30.5% | 713 | 24.0% |
| Multi-model (≥2 models) | 325 | 35.6% | 1949 | 65.6% |
| All 3 models | 100 | 10.9% | 859 | 28.9% |
| **All clusters** | 914 | 100% | 2972 | 100% |

## 5. Overlap of within-model vs cross-model consistency (finding-level)

Each finding is classified by whether it has within-model multi-run support and/or cross-model support.

| Category | Findings | Share of all findings |
|----------|----------:|----------------------:|
| Within-model **and** cross-model | 1803 | 60.7% |
| Within-model only (not cross-model) | 713 | 24.0% |
| Cross-model only (not within-model multi-run) | 146 | 4.9% |
| Neither (singleton-like for both axes) | 310 | 10.4% |
| **Total** | 2972 | 100% |

## 6. How to read these numbers

- **Within-model multi-run share** answers: “Of this model’s outputs, what fraction are defects it found again in another run?”
- **Cross-model share** answers: “Of the outputs, what fraction are defects also found by at least one other model?”
- **Jaccard** is stricter at the contract level: it compares *sets of defects* between two runs/models, so unique extras on either side lower the score.
- These stats use the **corrected** semantic clusters, so agreement is about defect identity after clustering/verify/reassign—not exact string match.

## 7. How many Qwen clusters needed DeepSeek correction?

After Qwen produced final clusters (`zeroshot_error_clusters_final_20.json`), DeepSeek audited multi-member clusters for coherence. Incoherent clusters were split and reassigned.

### 7.1 Cluster-level verify outcome

| Outcome | Clusters | Share of all clusters (829) | Share of multi-member checked (598) |
|---------|---------:|----------------------------:|------------------------------------:|
| Singleton (auto-pass) | 231 | 27.9% | — |
| Coherent (kept) | 445 | 53.7% | 74.4% |
| **Incoherent (needed correction)** | **128** | **15.4%** | **21.4%** |
| Failed / missing JSON | 25 | 3.0% | 4.2% |
| **Total** | **829** | 100% | checked: 598 |

- Coherent rate among checked multi-member clusters: **74.4%** (445/598).
- Problematic rate among checked multi-member clusters: **21.4%** incoherent (128/598), plus **4.2%** failed (25/598).

### 7.2 Finding-level: members that were split out for reassignment (“orphans”)

DeepSeek flagged inconsistent members inside incoherent clusters; the correction step removed **249** such findings and reassigned them.

| Quantity | Count | Share of all findings in the 20-contract error lists (2972) |
|----------|------:|-------------------------------------------------------------:|
| Findings inside incoherent clusters (whole cluster contaminated) | 699 | 23.5% |
| **Findings flagged & reassigned (orphans)** | **249** | **8.4%** |
| Findings in failed verify clusters | 202 | 6.8% |

Correction disposition of the 249 orphans:

| Action | Count | Share of orphans |
|--------|------:|-----------------:|
| Reassigned to an existing cluster | 148 | 59.4% |
| New cluster (model decision) | 73 | 29.3% |
| New cluster (parse-failure singleton fallback) | 28 | 11.2% |
| **Total orphans** | **249** | 100% |

### 7.3 Orphans as a share of each model’s error-list output

Denominator = that model’s zeroshot findings on the same 20 contracts (all 3 runs).

| Model | Model findings (3 runs) | Orphans from this model | Share of this model’s outputs | Findings sitting in incoherent clusters | Share of this model’s outputs |
|-------|------------------------:|------------------------:|------------------------------:|----------------------------------------:|------------------------------:|
| Fable 5 | 1024 | 62 | 6.1% | 237 | 23.1% |
| GPT-5.6 Sol | 1667 | 169 | 10.1% | 383 | 23.0% |
| Gemini 3.8 Flash | 281 | 18 | 6.4% | 79 | 28.1% |
| **All** | **2972** | **249** | **8.4%** | **699** | **23.5%** |

### 7.4 Reading guide

- **128 / 598 = 21.4%** of audited multi-member Qwen clusters were judged incoherent and required correction.
- Only the flagged members were moved (**249** orphans ≈ **8.4%** of all 2972 findings). The other members in those clusters were kept as the majority theme.
- If you instead count every finding that lived in an incoherent cluster before splitting, that pool is larger (contaminated neighborhood), shown in the table above.
- Failed verify clusters (25) were **not** auto-corrected in the orphan pipeline; they remained as originally clustered.
