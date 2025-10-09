

# Service

## Section

### Sub-section

**Bold**
*Italic*
***Bold Italic***

- Bullet point
  - Sub-bullet needs two spaces
* Asterisk bullet
+ Plus bullet

1. One
2. Two

- [x] Install dependencies
- [x] Run MetaPhlAn4
- [ ] Analyze strain variation
- [ ] Generate report

- **MetaPhlAn4**<br>Taxonomic profiling via marker genes  
- **StrainPhlAn4**<br>Strain-level analysis using marker variation  
- **HUMAnN3**<br>Functional profiling based on UniRef mappings

---

## 📘 About BioFigR

BioFigR provides advanced RNA-seq and metagenomic analysis pipelines with an emphasis on reproducibility, clarity, and visual interpretation.  
Each workflow is fully scripted in base R and tested on Linux systems.

**Website:** [https://biofigr.com](https://biofigr.com)  
**Contact:** stephen@biofigr.com

---

## Table of Contents
- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)
- [License](#license)

---

### 🔍 Key Features

- ✅ Marker-based profiling (MetaPhlAn4, StrainPhlAn4)
- ✅ Reproducible pipeline structure
- ✅ Automatic resume and skip logic
- ✅ Linux-native shell scripting (no external dependencies)

---

| Tool | Method | Output Type | Notes |
|------|---------|-------------|-------|
| MetaPhlAn4 | Marker-based | Taxonomic profiles | Fast and specific |
| StrainPhlAn4 | Marker-based | Strain trees | Requires MetaPhlAn markers |
| HUMAnN3 | Hybrid | Functional pathways | Combines marker and reference mapping |

---

```bash
# Example: run MetaPhlAn4 with default database
metaphlan sample.fastq.gz \
  --input_type fastq \
  --nproc 8 \
  --output_file sample_profile.txt
```

```R
# R example
files <- list.files("results", pattern = "*.tsv", full.names = TRUE)
```

<details>
<summary>Click to view workflow structure</summary>
project/
├── data/
├── results/
├── scripts/
│ ├── run_metaphlan.sh
│ ├── run_humann.sh
└── README.md
</details>

---

![Build](https://img.shields.io/badge/build-passing-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Platform](https://img.shields.io/badge/platform-Linux-lightgrey)

---

> **Note**  
> All example data and scripts in this repository are provided for demonstration.  
> For commercial use or collaboration inquiries, please contact **stephen@biofigr.com**.

_ _

> **Warning**  
> Running with `--nproc 1` will dramatically slow processing.

_ _

> **Tip**  
> Use a dedicated conda environment to isolate dependencies.

_ _

> **Caution**  
> Removing intermediate files will prevent resume functionality.

---

### 📚 Citation

If you use this workflow in your research, please cite:

> BioFigR (2025). *Reproducible MetaPhlAn4 and StrainPhlAn4 Pipelines.*  
> https://github.com/biofigr/metaphlan-strainphlan

---

| Type    | Example | Symbol               |
| ------- | ------- | -------------------- |
| Info    | 🧠      | `:brain:`            |
| Tip     | 💡      | `:bulb:`             |
| Warning | ⚠️      | `:warning:`          |
| Success | ✅      | `:white_check_mark:` |
| File    | 📄      | `:page_facing_up:`   |
| Folder  | 📁      | `:file_folder:`      |
| Time    | ⏱️      | `:stopwatch:`        |

---

© 2025 **BioFigR** — Reproducible pipelines for transparent bioinformatics.

---
