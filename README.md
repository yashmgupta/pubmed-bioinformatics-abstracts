# PubMed Bioinformatics Abstracts — Collection Pipeline

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/yashmgupta/pubmed-bioinformatics-abstracts/blob/main/bio_abs.ipynb)

A reproducible Jupyter notebook pipeline to collect, process, and publish **174,154** bioinformatics abstracts from PubMed (2020–2026) to the HuggingFace Hub for LLM fine-tuning.

**Curated by:** Dr. Yash M Gupta

---

## What This Does

1. Queries NCBI PubMed via the Entrez E-utilities API using Biopython
2. Fetches all journal articles tagged with `bioinformatics[MeSH Terms]` from 2020–2026
3. Extracts: PMID, title, abstract, year, journal, DOI
4. Saves results as JSONL slices (resumable — safe to re-run)
5. Merges slices into a HuggingFace `Dataset` and exports as Parquet
6. Pushes the dataset to the HuggingFace Hub

---

## Output Dataset

The resulting dataset is published at:  
**[huggingface.co/datasets/yashm/pubmed-bioinformatics-abstracts](https://huggingface.co/datasets/yashm/pubmed-bioinformatics-abstracts)**

| Column     | Type   | Description                       |
|------------|--------|-----------------------------------|
| `pmid`     | string | PubMed ID                         |
| `title`    | string | Article title                     |
| `abstract` | string | Full abstract text                |
| `year`     | string | Publication year                  |
| `journal`  | string | Journal name                      |
| `doi`      | string | DOI (if available)                |
| `source`   | string | Always `"pubmed"`                 |
| `slice`    | string | Time slice used during collection |

---

## Requirements

- Python 3.9+
- NCBI Entrez account (free) — [register here](https://www.ncbi.nlm.nih.gov/account/)
- HuggingFace account + write token — [get token here](https://huggingface.co/settings/tokens)

Install dependencies:
```bash
pip install biopython tqdm lxml datasets pandas pyarrow huggingface_hub
```

---

## Usage

### Option 1 — Google Colab (recommended)
Click the **Open in Colab** badge above. No local setup needed.

### Option 2 — Local
```bash
git clone https://github.com/yashmgupta/pubmed-bioinformatics-abstracts.git
cd pubmed-bioinformatics-abstracts
pip install biopython tqdm lxml datasets pandas pyarrow huggingface_hub
jupyter notebook bio_abs.ipynb
```

Before running, set your credentials as environment variables (or edit Step 2 in the notebook directly):

```bash
export NCBI_EMAIL="your_email@example.com"
export NCBI_API_KEY="your_ncbi_api_key"   # optional, but recommended
```

---

## Pipeline Overview

```
PubMed (Entrez API)
       │
       ▼
  Yearly/Monthly Slices   ←── resumable, skips already-downloaded slices
       │
       ▼
  JSONL files (out/slices_jsonl/)
       │
       ▼
  HuggingFace Dataset + Parquet
       │
       ▼
  HuggingFace Hub (public dataset)
```

---

## Notes

- The pipeline is **resumable** — if interrupted, re-running skips completed slices
- NCBI rate limit: ~3 requests/sec without API key, ~10/sec with one
- If any year has >9,999 results, the pipeline automatically splits into monthly sub-queries
- Only articles **with abstracts** are included

---

## License

Code: [MIT License](LICENSE)

Dataset: [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

Abstract text is sourced from PubMed/MEDLINE and subject to [NLM Terms and Conditions](https://www.nlm.nih.gov/databases/download/terms_and_conditions.html).

---

## Citation

If you use this pipeline or dataset, please cite:

```bibtex
@dataset{gupta_2020_pubmed_bioinformatics,
  author    = {Gupta, Yash M},
  title     = {PubMed Bioinformatics Abstracts (2020--2026)},
  year      = {2026},
  publisher = {Hugging Face},
  url       = {https://huggingface.co/datasets/yashm/pubmed-bioinformatics-abstracts}
}
```

> Sayers EW, et al. Database resources of the National Center for Biotechnology Information. *Nucleic Acids Research*. 2022;50(D1):D13–D25. doi:10.1093/nar/gkab1112
