# SUBMISSION.md
**Afaq Khan | Voxel Research & Analytics Intern | April 2026**

---

## What I Did

I treated this as two separate problems: first, audit the extraction pipeline; second, find genuine non-safety opportunities from what survived that audit.

The notebook walks through both in sequence. Rule-based checks came first because the obvious structural problems — missing timestamps, duplicate quotes across buckets, schema inconsistencies — do not need a language model to detect. After those were logged, I used the Claude API to normalize 687 unique labels into 135 canonical themes and score evidence quality across all 702 extracted use cases.

The audit came last, after the automated ranking was complete. I reviewed all 10 top-ranked non-safety opportunities manually, inspecting the member labels inside each cluster rather than just trusting the canonical name or the frequency count.

---

## Tools Used

- **Python** (pandas, matplotlib, json, re) for parsing, flattening, and rule-based checks
- **Claude API** (claude-sonnet-4-5) for label normalization and evidence quality scoring
- **json-repair** library to handle malformed JSON from LLM responses
- **Jupyter notebook** as the primary working environment
- **Matplotlib** for charts included in the memo

I split the label normalization into two API calls — safety labels first, non-safety second — to stay within token limits. This turned out to matter: the LLM caught only 4 of 207 cross-bucket contamination cases, because the two calls never saw both buckets simultaneously. Rule-based quote matching caught all 207. Both methods were necessary and neither alone was sufficient.

---

## Key Findings

**On pipeline quality:**

The pipeline is good at one thing: finding supporting quotes. Average evidence support scored 4.75 out of 5 across 702 use cases. The weaker spot is categorization. 48 of 99 files had the same quote appearing in both the safety and non-safety buckets. The pipeline processes a single conversation twice and never commits to one category. 222 evidence items were missing timestamps, 20 quotes were recycled across multiple labels in the same file, and 7 quotes contained likely transcription errors.

**On non-safety opportunities:**

The highest-ranked non-safety opportunity by raw frequency — Action Tracking and Accountability, 20 calls — was disqualified after manual review. All 22 of its member labels describe Voxel's existing safety workflow feature. Customers discussing it are describing current functionality, not a new opportunity.

The three genuine non-safety signals, after removing the false positive:

1. **Door Duration Monitoring** — 11 calls, 4.40/5 evidence. Operational cost play, distinct buyer (logistics managers), consistent signal across calls.
2. **Dashboard and UI Customization** — 8 calls, 5.00/5 evidence. Role-based views and gamification. Platform stickiness play.
3. **System Integration and Data Export** — 8 calls, 4.62/5 evidence. API and CSV integration with EHS and BI systems. Platform expansion play.

---

## Assumptions

- Files with zero safety use cases and only non-safety extractions were treated as admin/commercial noise and excluded from opportunity ranking. Four files met this criteria.
- Evidence quality scoring used a 1-5 scale for both evidence support and label accuracy. Use cases scoring below 2 on evidence support were excluded from ranking. Zero use cases were excluded on this basis, the pipeline consistently attached at least minimal evidence.
- Cross-bucket contamination was defined as the same quote appearing in both safety and non-safety buckets within the same file. Cross-file occurrences of the same label in different buckets were not flagged, that reflects real-world variance in how customers discuss the same feature.
- The singleton rate of 34% in the clustering output was treated as a known limitation. One missed grouping was identified manually: "Fire Door and Dock Door Monitoring" likely belongs with the Door Duration Monitoring cluster, which means the call count for that opportunity may be slightly understated.

---

## Limitations

- Layer 1 validation used 8 manually identified ground truth cases. A more robust test would use a stratified random sample of 20 to 30 files across different issue types.
- Audit verdicts for borderline clusters reflect one reviewer's judgment. A second reviewer might reach different conclusions on items marked borderline or mixed.
- The single-pass extraction fix recommended in the memo is a suggestion to Voxel's pipeline team, not something implemented in this analysis. The contamination was detected after the fact using rule-based matching.
- LLM outputs were validated but not exhaustively verified. Claude's generic flagging caught 2 of 5 manually identified noise cases and correctly left all 3 clean cases unflagged.

---

## Files in This Submission

| File | Description |
|---|---|
| `voxel_analysis.ipynb` | Main analysis notebook with all code and outputs |
| `voxel_memo.docx` | 1-2 page memo answering the two business questions |
| `voxel_slides.pdf` | Slide deck summarizing key findings |
| `annotated_use_cases.csv` | Full dataset with all flags and quality scores |
| `label_clusters.csv` | 135 canonical label clusters with generic and contamination flags |
| `opportunity_ranking.csv` | Ranked non-safety opportunities with audit verdicts |
| `SUBMISSION.md` | This file |

---

## Time Spent

Approximately 5 to 7 hours across two days. The analysis itself took around 3 hours. The remaining time went into the three-layer audit, manual file review, and writing up findings.
