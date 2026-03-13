---
layout: post
title: "Rapid and Reproducible RSSC Sequevar Typing with Snakemake"
date: 2026-03-13
categories: [bioinformatics, plant-pathology, snakemake]
tags: [ralstonia, rssc, sequevar, phylogenetics, snakemake, iqtree2, mafft, bacterial-genomics, reproducible-research, workflow-management]
author: Steve Baeyen
description: "A Snakemake pipeline for automated Ralstonia solanacearum species complex (RSSC) sequevar typing based on the endoglucanase (egl) gene — from genome assemblies to phylogenetic tree and sequevar assignment in one command."
giscus_comments: true
---

## Automated sequevar typing for Ralstonia solanacearum from genome to assignment in one command

---

## Background: Why Sequevar Typing Matters

*Ralstonia solanacearum* species complex (RSSC) is one of the most destructive soilborne plant pathogens worldwide — responsible for bacterial wilt on hundreds of host species including potato, tomato, banana, and ginger. Accurate strain characterisation is critical for:

- **Epidemiological tracking** across geographic regions and timepoints
- **Quarantine and regulatory decisions** at borders and in diagnostics labs
- **Host range and virulence prediction** based on known sequevar biology

The gold standard for RSSC subspecies classification uses two complementary systems:
- **Phylotypes** (I–IV + IIA/IIB) — based on geographic/genomic lineage
- **Sequevars** — fine-grained groups within phylotypes, defined by sequence diversity in the endoglucanase (*egl*) gene

Traditionally, sequevar assignment involved manual PCR, Sanger sequencing, multiple alignment, tree construction, and visual inspection — a process that does not scale to modern genomic datasets.

## The Solution: RSSC_sequevar_typing Pipeline

The **[RSSC_sequevar_typing](https://gitlab.ilvo.be/stevebaeyen/rssc_sequevar_typing)** Snakemake pipeline fully automates this workflow. Starting from bacterial genome assemblies (`.fna`), it extracts the *egl* gene, performs phylogenetic analysis against 58 curated reference sequevars, and delivers both an annotated tree and a tabular sequevar assignment — all in a single reproducible run.

```bash
conda activate snakemake
snakemake --use-conda -j 8
```

---

## Pipeline Overview

The workflow consists of 7 rules that take genome FASTA files to a sequevar assignment table:

```
data/*.fna
     │
     ▼
 ┌──────────────┐
 │ 1. run_epcr  │  In-silico PCR — extract egl amplicons from all genomes
 └──────┬───────┘
        ▼
 ┌──────────────────┐
 │ 2. extract_egl   │  Split batch ePCR output into per-sample FASTA files
 └──────┬───────────┘
        ▼
 ┌──────────────┐
 │ 3. trim_egl  │  Trim to ~698 nt phylogenetically informative region
 └──────┬───────┘
        ▼
 ┌──────────────┐
 │ 4. align_egl │  MAFFT alignment (queries + 58 reference sequevars)
 └──────┬───────┘
        ▼
 ┌──────────────┐
 │ 5. build_tree│  IQ-TREE2 ML tree (TIM+I+G4, 1000 UFBoot2 replicates)
 └──────┬───────┘
        │
        ├─────────────────────────────────────┐
        ▼                                     ▼
 ┌────────────────────┐   ┌──────────────────────────────┐
 │ 6. visualize_tree  │   │ 7. assign_sequevar            │
 │ ggtree PDF with    │   │ Nearest-reference tip lookup  │
 │ sequevar annotation│   │ → sequevar_assignments.txt    │
 └────────────────────┘   └──────────────────────────────┘
```

Steps 2–3 run **in parallel** across all samples. Steps 6 and 7 also run in **parallel** once the tree is built.

---

## Step-by-Step Breakdown

### Step 1 — In-silico PCR (`run_epcr`)

The *egl* gene is extracted from each genome assembly using primer-based in-silico PCR via the companion [`run_epcr.py`](https://gitlab.ilvo.be/stevebaeyen/run_epcr) script.

| Parameter | Value |
|---|---|
| Forward primer | `ATGCATGCCGCTGGTCGCCGC` |
| Reverse primer | `GCGTTGCCCGGCACGAACACC` |
| Max amplicon length | 2000 bp |
| Max mismatches | 2 |
| Max indels | 0 |

Output: `results/00_epcr/all_amplicons.fasta`

### Step 2 — Extract per-sample egl (`extract_egl_gene`)

The batch ePCR FASTA is split into individual per-sample files by matching the sample name in the amplicon header. Runs in parallel across all samples.

### Step 3 — Trim to phylogenetic region (`trim_egl`)

The raw amplicon (~900–1100 bp) is trimmed to the **~698 nt phylogenetically informative region** using motif-based trimming. This region is the standard used in all sequevar classification literature.

| Motifs | Values |
|---|---|
| START patterns | `ACCGCG`, `ACCGTG`, `ACGGCG`, `ACGGTG`, `ACTGCG`, `ATGGTG`, `CGGGCG` |
| END pattern | `CAGTGG` |
| Expected size | 678–710 nt |

When multiple START motifs match, disambiguation uses upstream markers (`AGCCT`, `ACCCT`, `AGTTT`) found within 15 nt before the candidate.

### Step 4 — MAFFT alignment (`align_egl`)

Trimmed query sequences are aligned together with the **58 curated reference sequevar sequences** from the [CIRAD dataverse](https://doi.org/10.18167/DVN1/CUWA5P) using MAFFT (`--auto --reorder`).

### Step 5 — Maximum-likelihood tree (`build_tree`)

A phylogenetic tree is inferred with **IQ-TREE2** using the `TIM+I+G4` substitution model — selected by prior jModelTest2 analysis of the *egl* reference sequences — with 1000 ultrafast bootstrap (UFBoot2) replicates.

| Parameter | Value |
|---|---|
| Tool | IQ-TREE2 ≥ 2.2 |
| Model | TIM+I+G4 |
| Bootstrap | 1000 UFBoot2 |

### Step 6 — Annotated tree visualization (`visualize_tree`)

An annotated, publication-ready tree is rendered as a PDF using **ggtree** in R. Tip labels are colour-coded by phylotype:

| Phylotype | Colour |
|---|---|
| I | Red |
| IIA | Blue |
| IIB | Green |
| III | Purple |
| IV | Orange |
| Query | Black |

Bootstrap support values ≥ 70% are shown on internal nodes.

### Step 7 — Sequevar assignment (`assign_sequevar`)

Each query genome is assigned a phylotype and sequevar by finding the **closest reference tip** in the ML tree using patristic (branch-length) distances. The output is a tab-delimited table:

| Column | Description |
|---|---|
| Genome | Query genome name (.fna file stem) |
| Phylotype | Assigned RSSC phylotype (I, IIA, IIB, III, IV) |
| Sequevar | Assigned egl sequevar number |
| Closest_Reference | FASTA ID of the nearest reference tip |
| Tree_Distance | Patristic distance to the closest reference |

> **Note:** A `Tree_Distance` > 0.05 may indicate a divergent or novel genotype. Always inspect the tree visualization alongside the assignment table.

---

## Output Files

| Output | Path |
|---|---|
| ePCR amplicons (batch) | `results/00_epcr/all_amplicons.fasta` |
| Per-sample egl genes | `results/01_egl_genes/{sample}.egl.fasta` |
| Trimmed egl sequences (~698 nt) | `results/02_egl_genes_trimmed/{sample}.egl.trimmed.fasta` |
| Multiple sequence alignment | `results/03_alignment/egl_aligned.fasta` |
| ML tree (Newick) | `results/04_trees/IQTree2.egl.tree` |
| Annotated tree visualization | `tree_visualizations/IQTree2.egl.tree.visualization.pdf` |
| Sequevar assignments | `results/05_sequevar_assignments/sequevar_assignments.txt` |

---

## Quick Start

### 1. Clone and place genomes

```bash
git clone https://gitlab.ilvo.be/stevebaeyen/rssc_sequevar_typing.git
cd rssc_sequevar_typing
cp /path/to/your/genomes/*.fna data/
```

### 2. Run the pipeline

```bash
conda activate snakemake
snakemake --use-conda -j 8
```

### 3. Preview without running

```bash
snakemake -n -p
```

### 4. Run on a SLURM cluster

```bash
snakemake --use-conda -j 100 \
    --cluster "sbatch -p batch -c {threads} --mem=4G -t 01:00:00" \
    --configfile config.yaml
```

---

## Configuration

All parameters are centralised in `config.yaml`:

```yaml
data_dir: "data"

epcr:
  max_length: 2000
  mismatches: 2
  indels: 0

trimming:
  start_patterns: [ACCGCG, ACCGTG, ACGGCG, ACGGTG, ACTGCG, ATGGTG, CGGGCG]
  end_pattern: CAGTGG

iqtree2:
  model: "TIM+I+G4"
  bootstrap: 1000

resources:
  epcr_threads: 32
  mafft_threads: 16
  iqtree_threads: 32
```

---

## Requirements

- [Snakemake](https://snakemake.readthedocs.io/) ≥ 7.0
- [Conda](https://docs.conda.io/) or [Mamba](https://mamba.readthedocs.io/)

All tool dependencies (MAFFT, IQ-TREE2, ggtree, Biopython, etc.) are installed automatically via per-rule conda environments. No manual dependency management required.

---

## Citation

If you use this pipeline in your research, please cite:

> Baeyen, S. (2026). *Snakemake pipeline for Ralstonia solanacearum species complex (RSSC) sequevar typing based on the endoglucanase (egl) gene* (Version 1.0.0). Zenodo. [https://doi.org/10.5281/zenodo.19002750](https://doi.org/10.5281/zenodo.19002750)

---

## References

- Cellier, G., Pecrix, Y., Gauche, M. M., & Cheron, J. J. (2023). Ralstonia solanacearum species complex egl reference database. CIRAD Dataverse. [https://doi.org/10.18167/DVN1/CUWA5P](https://doi.org/10.18167/DVN1/CUWA5P)
- Fegan, M. & Prior, P. (2005). How complex is the *Ralstonia solanacearum* species complex? In: *Bacterial Wilt Disease and the Ralstonia solanacearum Species Complex*, APS Press.
- Wicker, E. et al. (2007). Ralstonia solanacearum strains from Martinique exhibiting a new pathogenic potential. *Applied and Environmental Microbiology* 73(21):6790–6801.
- Nguyen, L.-T. et al. (2015). IQ-TREE: A fast and effective stochastic algorithm for estimating maximum-likelihood phylogenies. *Molecular Biology and Evolution* 32(1):268–274.
- Yu, G. et al. (2017). ggtree: an R package for visualization and annotation of phylogenetic trees with their covariates and other associated data. *Methods in Ecology and Evolution* 8(1):28–36.

---

**Pipeline repository:** [https://gitlab.ilvo.be/stevebaeyen/rssc_sequevar_typing](https://gitlab.ilvo.be/stevebaeyen/rssc_sequevar_typing)  
**License:** MIT  
**Author:** Steve Baeyen — ILVO Plant Sciences Unit
