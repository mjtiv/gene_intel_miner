# Gene Intel Miner

## Overview
**Note:** No installation required. This `.aix` file runs entirely inside ChatGPT with `.aix` parsing enabled. Just load the file, choose `local` or `live`, and follow the prompts.

**Gene Intel Miner** is a live data-mining workflow for rapid triage of gene targets.  
It uses a structured `.aix` file to pull biomedical intelligence from public APIs, merge the results, and output decision-ready tables with source citations.

The workflow is optimized for:
- **Speed** — choose between `fast`, `standard`, or `deep` modes.
- **Reproducibility** — consistent schema, auditable logs.
- **Flexibility** — run any HGNC gene list with optional disease context and date cutoffs.

---

## Features
- **Prompted input** — accepts comma-separated gene lists and settings.
- **Multiple depth modes**:
  - **fast**: minimal core intel
  - **standard**: full core fields
  - **deep**: maximum detail (trial phases, label changes, patents)
- **Live data sources**:
  - [OpenTargets](https://www.opentargets.org/)
  - [ClinicalTrials.gov](https://clinicaltrials.gov/)
  - [FDA Labels & PMAs](https://www.fda.gov/)
  - [PubMed](https://pubmed.ncbi.nlm.nih.gov/)
- **Outputs**:
  - Excel or CSV table
  - Optional JSON audit log
  - Per-cell citations

---

## Example Run

### Input
```
Run with: TP53, EGFR — mode: deep — context: (none)
```

### Output
| Gene | Mechanism | On-market targeted drugs | Companion Diagnostics | Landmark trials (NCT) | Recent label changes |
|---|---|---|---|---|---|
| TP53 | Tumor suppressor TF; genome integrity | None (direct); APR-246 failed; MDM2 inhibitors halted (idasanutlin) | None | — | — |
| EGFR | RTK; ligand→dimerization→TK signaling | Gefitinib, Erlotinib, Afatinib, Dacomitinib, **Osimertinib**; (mAbs: Cetuximab, Panitumumab) | cobas EGFR v2, Guardant360 CDx, FoundationOne CDx/Liquid | ADAURA, LAURA | 2024 Tagrisso updates |

---

## Usage
1. Drop this file in ChatGPT.
2. Type the following command:
```
Parse this .aix file and run it live — prompt me for inputs, fetch the data, and then format the full results.
```
3. Provide inputs when prompted (gene list, depth mode, optional disease/date/export name).
4. Wait for the summary table.
5. Export to Excel or CSV:
```
export as Excel
```
or
```
expand columns
```

See [USAGE.md](USAGE.md) for full instructions.

---

## Tips
- Large gene lists in **deep** mode can hit API timeouts; try `fast` or `standard` first.
- Licensed sources like DrugBank require your own credentials.
- Merge multiple runs easily due to consistent output schema.

---

## License
This workflow is intended for research and informational purposes.  
Check each API's terms of use before redistributing or storing data.


## Mode & Column Mapping

| Field / Column | Fast Mode | Standard Mode | Deep Mode | Primary Data Sources |
|---|---|---|---|---|
| **Gene** | ✅ | ✅ | ✅ | HGNC, internal list |
| **Mechanism of Action** | ✅ | ✅ | ✅ | OpenTargets, NCBI Gene, PubMed |
| **On-market targeted drugs** | ✅ (basic) | ✅ (with drug names) | ✅ (with generation/class) | FDA Labels, OpenTargets, ChEMBL, PubMed |
| **Drug Class** | ❌ | ✅ | ✅ | ChEMBL, PubMed, literature |
| **Known failed/terminated drugs** | ❌ | ✅ | ✅ | ClinicalTrials.gov, PubMed |
| **Companion Diagnostics** | ❌ | ✅ | ✅ (with PMA IDs) | FDA PMA database, manufacturer sites |
| **Top trial IDs** | ❌ | ✅ | ✅ (with phases and NEJM links if available) | ClinicalTrials.gov |
| **Trial Phases** | ❌ | ❌ | ✅ | ClinicalTrials.gov |
| **Recent Label Changes** | ❌ | ❌ | ✅ | FDA Drug Label database |
| **Selectivity Profile** | ❌ | ✅ (basic) | ✅ (detailed) | ChEMBL, OpenTargets |
| **Patent / Exclusivity Notes** | ❌ | ❌ | ✅ | FDA Orange Book |
| **Key Biomarkers** | ❌ | ✅ | ✅ | OpenTargets, literature |
| **Companion Diagnostics Notes** | ❌ | ✅ | ✅ (expanded) | FDA PMA, manufacturer docs |

**Notes:**
- **Fast mode** — minimal, quick-scan fields for many genes; skips heavy API calls like ClinicalTrials.gov full trial metadata or Orange Book lookups.
- **Standard mode** — adds drug class detail, failed drugs, biomarkers, CDx, and top trial IDs.
- **Deep mode** — adds trial phases, label changes, patent/exclusivity info, expanded selectivity profiles, and PMA-level CDx details.
- If a source is slow or unreachable, the field may be blank in that run but can be filled in later by rerunning.


---

## Data Source Modes (v0.3.4)
Set `data_source_mode` when you run:

- `local` — **fast triage for huge lists.** Uses ChatGPT’s built‑in biomedical knowledge and session context. No live API calls; may be outdated; no per‑cell citations.
- `live` — **fully up‑to‑date deep dive.** Calls OpenTargets, ClinicalTrials.gov, FDA labels/PMA, PubMed, etc. Slower; includes citations and fresh label/trial data.

**Recommended workflow**
1. **Local (fast triage):** run hundreds/thousands of genes to shortlist hits.
2. **Live (deep dive):** re-run the top 5–20 genes with `data_source_mode: live` for current trials, labels, and patents.

**Example**
```text
Run with: TP53, EGFR, PIK3CA — mode: deep — data_source_mode: local
```

