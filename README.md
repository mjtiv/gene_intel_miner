# Gene Intel Miner

## Overview
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

## Installation & Requirements
- Python 3.8+
- Internet access for live lookups
- `.aix` runner (or ChatGPT with `.aix` parsing ability)

Clone this repository:
```bash
git clone https://github.com/yourusername/gene-intel-miner.git
cd gene-intel-miner
```

---

## Usage
1. Place the `.aix` file in your working directory.
2. Parse and run:
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
