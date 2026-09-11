# Contract review SME sheet (top 3 errors)

SME subset: contracts **2, 5, 6, 7, 8, 10, 13, 14, 16, 21** (10 contracts).


Public mirror for SME review. The main experiment repo is private.

## Files

- [`sme_review_top3.xlsx`](sme_review_top3.xlsx) — Excel spreadsheet for SME comments (wrapped text)
- [`sme_review_top3.tsv`](sme_review_top3.tsv) — same data as TSV
- [`contracts/`](contracts/) — one markdown file per contract (top 3 errors + full text)

## TSV columns

1. `link_to_contract_markdown` (first)
2. `contract_no`
3. `error_1` / `comment_1`
4. `error_2` / `comment_2`
5. `error_3` / `comment_3`

Error cells use: `Title: summary`. Comment columns are blank for SMEs.

## Spreadsheet link

https://github.com/MaryHu-YR/contract-review-sme-top3/blob/main/sme_review_top3.tsv

## Consistency stats

- [`zeroshot_consistency_stats_20_brief.md`](zeroshot_consistency_stats_20_brief.md) — key percentages
- [`zeroshot_consistency_stats_20.md`](zeroshot_consistency_stats_20.md) — full stats with counts
