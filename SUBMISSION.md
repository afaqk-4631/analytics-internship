# SUBMISSION.md
**Afaq Ahmed | Voxel Research & Analytics Intern | April 2026**

---

## What I Did

I split this into two jobs. First: audit the pipeline and figure out how much to trust the output. Second: find real non-safety opportunities from whatever survived that audit.

Rule-based checks came first. Missing timestamps, duplicate quotes across buckets, recycled evidence within files, transcription errors. None of that needs a language model. It needs a loop and a hash check. After those were flagged, I used the Claude API to cluster 687 unique labels into 135 canonical themes and score evidence quality across all 702 use cases.

The ranking came after all of that. I didn't stop at the automated frequency count. I went through the top 10 non-safety clusters manually and looked at what labels were actually grouped inside each one. That's where the most important finding came from.

---

## Tools Used

- Python (pandas, re, json, matplotlib) for parsing, flattening, and rule-based checks
- Claude API (claude-sonnet-4-5) for label normalization and evidence quality scoring
- json-repair to handle broken JSON from LLM responses
- Jupyter as the working environment

One thing worth flagging: I split the label normalization into two separate API calls, safety labels first and non-safety second, because a single call hit token limits at around 62,000 characters. That decision had a real consequence. The LLM caught only 4 of 207 cross-bucket contamination cases. The reason is straightforward. When the two calls never see each other, cross-bucket overlap is invisible to the model. Rule-based quote matching caught all 207. This isn't a model failure. It's a design constraint I introduced to manage token limits, and it's why both approaches were needed.

---

## Key Findings

**Pipeline quality**

The pipeline does one thing well: it finds supporting quotes. Average evidence support scored 4.75 out of 5 across all 702 use cases. That's genuinely good. The problem is categorization. 48 of 99 files had the same quote appearing in both the safety and non-safety bucket. The pipeline reads a conversation twice and never has to choose. On top of that: 222 evidence items had no timestamp, 20 quotes were recycled across multiple labels in the same file, and 7 quotes had likely transcription errors ("person to pet" instead of "person to PIT").

**Non-safety opportunities**

Action Tracking and Accountability ranked first by raw frequency with 20 calls. I disqualified it after looking at all 22 of its member labels. Every single one describes Voxel's existing safety workflow feature. Customers talking about it are describing something that already exists, not asking for something new.

After removing that false positive, three genuine signals remain:

1. **Door Duration Monitoring** — 11 calls, 4.40/5 evidence. Operations and logistics managers want to track dock door dwell times to protect dock levelers and improve throughput. Clean signal, distinct buyer, consistent across calls.
2. **Dashboard and UI Customization** — 8 calls, 5.00/5 evidence. Customers want role-based views, shift dashboards, and gamification. Platform stickiness play.
3. **System Integration and Data Export** — 8 calls, 4.62/5 evidence. Customers want to push Voxel data into EHS systems and BI platforms via API or CSV. Platform expansion play.

---

## Assumptions

Four files had only non-safety extractions and no safety use cases at all. I treated those as admin or commercial calls and excluded them from the opportunity ranking.

Evidence quality was scored on a 1-5 scale. I excluded any use case scoring below 2 on evidence support. Zero use cases were excluded on this basis. The pipeline consistently attached at least some evidence to every extraction.

Cross-bucket contamination was defined as the same quote appearing in both buckets within the same file. Same label appearing in different files across different buckets was not flagged. That's real-world variance, not a pipeline error.

The 34% singleton rate in the clustering output was treated as a known limitation rather than a failure. One missed grouping was identified manually: "Fire Door and Dock Door Monitoring" likely belongs with the Door Duration Monitoring cluster, which means that opportunity's call count is probably understated.

---

## Limitations

The ground truth sample in Layer 1 validation was 8 cases. That's small. A proper test would use a stratified random sample of 20 to 30 files covering different issue types, not just the ones I happened to inspect early in the process.

Borderline cluster verdicts reflect one reviewer's judgment. A second reviewer might reasonably land differently on "Safety Analytics and Reporting" or "Intervention Impact Tracking."

The single-pass extraction fix in the memo is a recommendation to Voxel's pipeline team. It's not something I built. The contamination in this analysis was detected after the fact using rule-based quote matching.

Claude's generic flagging caught 2 of 5 manually identified noise cases. It correctly left all 3 clean cases unflagged. The LLM output was spot-checked but not exhaustively verified.

---

## Files in This Submission

| File | Description |
|---|---|
| `voxel_analysis.ipynb` | Main analysis notebook |
| `Afaq_Khan_Voxel_Slide_deck.pdf` | Slide deck answering the business questions |
| `annotated_use_cases.csv` | Full dataset with all flags and quality scores |
| `label_clusters.csv` | 135 canonical clusters with generic and contamination flags |
| `opportunity_ranking.csv` | Ranked non-safety opportunities with audit verdicts |
| `SUBMISSION.md` | This file |

---

## Time Spent

8 to 10 hours across two days. The core analysis took around 3 hours. The rest went into the three-layer audit, manual file review, and writing.
