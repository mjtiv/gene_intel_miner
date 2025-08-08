# Gene Intel Miner — Live Run Instructions

## Purpose
The `.aix` file contains a structured workflow that:
- Prompts the user for a **gene list** and optional context settings  
- Runs **live data lookups** from public biomedical APIs (OpenTargets, ClinicalTrials.gov, FDA, PubMed, etc.)  
- Aggregates the results into a **decision-ready table** (CSV/Excel) plus an audit log of sources  
- Handles multiple depth modes:  
  - **fast** — minimal fields, for quick scans  
  - **standard** — full core fields, for typical panel sizes  
  - **deep** — maximum fields (includes trial IDs, exclusivity notes, recent label changes)  

---

## Key Part (How It Works)
The essential functionality is:

```pseudo
1. Parse the `.aix` YAML — this defines:
   - What inputs to request (gene list, depth_mode, disease_context, date_cutoff, export_name)
   - Which APIs to call and how
   - What output columns to produce
   - Any UX warnings or runtime checks

2. Prompt the user for inputs:
   - Gene list (comma-separated HGNC symbols)
   - Depth mode (fast / standard / deep)
   - Optional: disease context, date cutoff, export name

3. Execute the pipeline:
   - Fetch live intel per gene from defined APIs
   - Apply formatting rules and merge into a single results table
   - Log all source URLs and timestamps for auditability

4. Save and export:
   - Excel and/or CSV table
   - Optional JSON audit log
```

The **real “key”** that makes this work is the `.aix` file’s **`inputs`** and **`pipelines`** sections.  
These control *exactly* what’s asked, which APIs are hit, and how results are transformed into the table.  
Once parsed, the runner simply **maps your answers to API calls**, waits for results, and formats them.

---

## Step-by-Step Usage

1. **Drop the `.aix` file into your environment**  
   Make sure your runner (Python or another `.aix`-aware host) can read the file.

2. **Run the parser and prompt**  
   Example in ChatGPT or your runner:
   ```
   Parse this .aix file and run it live — prompt me for inputs, fetch the data, and then format the full results.
   ```

3. **Provide inputs when prompted**  
   Example:
   ```
   Run with: TP53, EGFR — mode: deep — context: (none)
   ```

4. **Wait for processing**  
   Deep mode may take longer because it pulls:
   - Mechanism of Action
   - Known drugs (marketed, failed)
   - Biomarkers / Companion diagnostics
   - Top trial IDs + phases
   - Recent label changes
   - Selectivity profile
   - Orange Book patent/exclusivity notes (if available)

5. **Get your results in chat**  
   The runner will display a **summary table** in the conversation.

6. **Export to Excel/CSV**  
   Example in ChatGPT:
   ```
   export as Excel
   ```
   or  
   ```
   expand columns
   ```
   to add drug classes, trial phases, and patent notes.

7. **Download the file**  
   You’ll get a link like:  
   ```
   [Download the expanded Excel file](sandbox:/mnt/data/...)
   ```

---

## Tips
- **Deep mode + long gene lists** → may hit API timeouts; try *fast* or *standard* for large panels.  
- **Licensed sources** like DrugBank require credentials; the runner will default to public sources unless toggled.  
- If an API is slow or down, the run may skip that field and log a warning.  
- Output structure is consistent — so you can merge multiple runs easily.


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

