# Zeroshot consistency — key percentages (20 contracts)

Source: corrected semantic clusters (`zeroshot_error_clusters_final_20_corrected_scored.json`) + DeepSeek verify/correct summaries.  
Agreement = same final cluster. All numbers are **percentages only**.

---

## 1. Within-model (3 runs)

Share of each model’s own outputs that also appear (same defect) in another run of the same model.

*How stable is one model when you re-run it three times?* Higher = more of its findings repeat across runs.

- **Multi-run consistent**: the finding’s cluster also contains the **same model in at least one other run** (2 or 3 runs). Example: present in run1+run2 but not run3 → still counts.
- **All 3 runs**: stricter — the **same model appears in run1, run2, and run3** in that cluster. Two-run-only cases do **not** count. So this column is always ≤ Multi-run consistent.

| Model | Multi-run consistent | All 3 runs | Mean pairwise run Jaccard |
|-------|---------------------:|-----------:|---------------------------:|
| Fable 5 | 87.7% | 71.4% | 65.4% |
| GPT-5.6 Sol | 82.7% | 62.0% | 59.8% |
| Gemini 3.8 Flash | 85.1% | 64.1% | 64.3% |
| **All** | **84.7%** | **65.4%** | **63.2%** |

Pairwise run Jaccard:

*For each pair of runs, how much do their defect sets overlap on a contract (averaged)?* Jaccard is stricter than the “multi-run consistent” column above because unique extras on either side count against you.

| Model | run1↔run2 | run1↔run3 | run2↔run3 |
|-------|----------:|----------:|----------:|
| Fable 5 | 64.4% | 66.9% | 64.9% |
| GPT-5.6 Sol | 59.0% | 60.2% | 60.1% |
| Gemini 3.8 Flash | 64.8% | 60.8% | 67.4% |

---

## 2. Cross-model

Share of outputs that land in a cluster with ≥2 models (or all 3).

*How often do different models independently find the same defect?* “≥2 models” = at least one other model agrees; “All 3 models” = all three agree.

| Scope | ≥2 models | All 3 models |
|-------|----------:|-------------:|
| All findings | 65.6% | 28.9% |
| Fable 5 | 78.3% | 29.8% |
| GPT-5.6 Sol | 52.9% | 19.2% |
| Gemini 3.8 Flash | 94.3% | 83.3% |

Pairwise model Jaccard:

*Contract-level overlap of defect sets between two models (averaged).* Lower than the finding-level “≥2 models” rates because each model’s unique findings shrink the Jaccard.

| Pair | Jaccard |
|------|--------:|
| Fable 5 ↔ GPT-5.6 Sol | 34.7% |
| Fable 5 ↔ Gemini 3.8 Flash | 23.8% |
| GPT-5.6 Sol ↔ Gemini 3.8 Flash | 23.8% |

---


## 2b. Missed by one model (found by the other two)

*Look at defects that the **other two models both found**, but **this model missed**.*

Example for Gemini: Fable and GPT both have the defect in the same cluster; Gemini has no finding in that cluster.

| Column | Meaning |
|--------|---------|
| **Of all defects** | Among **all** defects (all clusters), what % are “other two found it, this model missed”. Small if misses are rare overall. |
| **Of defects both others found** | Among defects that **already have both other models**, what % is this model missing. This is the main “miss rate when the other two agree”. |
| **Of all findings (weighted)** | Same missed defects, but weighted by how many findings sit in those clusters, as % of **all** findings. Larger clusters count more. |

| Model | Of all defects | Of defects both others found | Of all findings (weighted) |
|-------|---------------:|-----------------------------:|---------------------------:|
| Fable 5 | 0.9% | 7.4% | 1.0% |
| GPT-5.6 Sol | 1.0% | 8.3% | 1.4% |
| Gemini 3.8 Flash | 22.8% | 67.5% | 34.3% |

*Read the middle column first:* when the other two models agree on a defect, Gemini is missing **67.5%** of the time; Fable/GPT only about **7–8%**.


## 2c. Coverage of the 3-model defect union

*Take the **union** of all semantic defects (clusters) found by any model — 914 clusters.*  
Each model’s share = fraction of that union where the model contributed at least one finding.  
(Also shown: each model’s share of raw finding volume, which sums to 100%.)

| Model | Share of defect union (clusters) | Share of all findings |
|-------|---------------------------------:|----------------------:|
| Fable 5 | 48.2% | 34.5% |
| GPT-5.6 Sol | 84.0% | 56.1% |
| Gemini 3.8 Flash | 14.2% | 9.5% |

*Note: cluster-union shares **do not sum to 100%** (multi-model clusters count for each participating model). Finding shares **do** sum to 100%.*

## 3. Within-model vs cross-model (finding mix)

*Split every finding into one bucket:* repeats across runs of the same model, agrees with another model, both, or neither.

| Category | Share of all findings |
|----------|----------------------:|
| Within-model **and** cross-model | 60.7% |
| Within-model only | 24.0% |
| Cross-model only | 4.9% |
| Neither | 10.4% |

---

## 4. Cluster composition

*What kinds of clusters did we end up with?* Left = share of cluster objects; right = share of all findings sitting in those clusters (multi-model clusters hold more findings).

| Cluster type | Share of clusters | Share of findings |
|--------------|------------------:|------------------:|
| Singleton (1 finding) | 33.9% | 10.4% |
| Multi-finding, single-model | 30.5% | 24.0% |
| Multi-model (≥2) | 35.6% | 65.6% |
| All 3 models | 10.9% | 28.9% |

---

## 5. Qwen clusters that needed DeepSeek correction

*After Qwen clustering, how often did DeepSeek say the cluster was mixed and needed fixing?*

| Metric | Percentage |
|--------|-----------:|
| Incoherent among multi-member checked | 21.4% |
| Coherent among multi-member checked | 74.4% |
| Verify failed among multi-member checked | 4.2% |
| Incoherent among **all** clusters | 15.4% |
| Findings flagged & reassigned (orphans) / all findings | 8.4% |
| Findings that sat in incoherent clusters / all findings | 23.5% |

Orphans as share of each model’s outputs:

*Of each model’s findings, what fraction were the ones DeepSeek pulled out for reassignment?* The second column is looser: any finding that lived in a bad cluster before the split.

| Model | Orphans / model outputs | In incoherent clusters / model outputs |
|-------|------------------------:|---------------------------------------:|
| Fable 5 | 6.1% | 23.1% |
| GPT-5.6 Sol | 10.1% | 23.0% |
| Gemini 3.8 Flash | 6.4% | 28.1% |
| **All** | **8.4%** | **23.5%** |

Orphan reassignment mix:

*After splitting, where did those flagged findings go?*

| Action | Share of orphans |
|--------|-----------------:|
| Merged into existing cluster | 59.4% |
| New cluster (model decision) | 29.3% |
| Singleton fallback | 11.2% |
