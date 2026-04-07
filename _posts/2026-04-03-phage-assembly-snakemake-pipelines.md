---
layout: post
title: "Two New Snakemake Pipelines for Bacteriophage Assembly and QC: Illumina and Nanopore"
date: 2026-04-03
author: "Steve Baeyen"
categories: [bioinformatics, genomics, workflows]
tags: [snakemake, bacteriophage, illumina, nanopore, assembly, quality-control]
description: "Two new Snakemake pipelines for bacteriophage genome assembly and quality control: one for Illumina reads and one for Nanopore reads."
giscus_comments: true
tools:
  - name: "phage_assembly_snakemake"
    url: "https://gitlab.ilvo.be/stevebaeyen/phage_assembly_snakemake"
    description: "Snakemake pipeline for bacteriophage genome assembly from Illumina paired-end reads using Shovill; includes fastp QC, QUAST, VirSorter2, CheckV, and automated HTML/PDF reports."
  - name: "phage-nanopore-assembly-snakemake"
    url: "https://gitlab.ilvo.be/stevebaeyen/phage-nanopore-assembly-snakemake"
    description: "Snakemake pipeline for bacteriophage genome assembly from Oxford Nanopore reads using Flye with Medaka polishing; includes NanoPlot QC, Filtlong, Porechop_ABI, Bandage, VirSorter2, CheckV, and automated HTML/PDF reports."
---

## A New Step for Reproducible Phage Genomics

I recently released two complementary Snakemake pipelines for bacteriophage genome assembly and quality control:

- [Phage Nanopore Assembly and QC Snakemake](https://gitlab.ilvo.be/stevebaeyen/phage-nanopore-assembly-snakemake)
- [Phage Illumina Assembly and QC Snakemake](https://gitlab.ilvo.be/stevebaeyen/phage_assembly_snakemake)

Together, they provide a reproducible, modular framework for transforming raw sequencing reads into biologically interpretable phage genome quality reports.

Both workflows are released under the MIT license and built for transparent, reproducible analysis using per-rule conda environments in Snakemake.

If you use these pipelines in your research, I’d love to hear about it! Feedback and contributions are always welcome.

---

## Why These Pipelines Matter

Bacteriophage projects often involve heterogeneous sequencing strategies. Some datasets are generated with high-accuracy short reads, others with long-read platforms, and many projects now combine both across multiple studies.

These pipelines are designed to make that reality easier to manage:

- One workflow optimized for Illumina paired-end reads
- One workflow optimized for Oxford Nanopore long reads
- Consistent reporting principles across both
- Scalable execution from laptop to HPC
- Reproducibility by design through Snakemake and isolated software environments

The goal is simple: spend less time stitching tools together, and more time interpreting phage biology.

---

## Pipeline 1: Illumina Phage Assembly and QC

Repository: [phage_assembly_snakemake](https://gitlab.ilvo.be/stevebaeyen/phage_assembly_snakemake)

This workflow starts from paired-end Illumina FASTQ files and performs:

1. Read QC and filtering with `fastp`
2. De novo assembly with `Shovill` (SPAdes backend)
3. Assembly metrics with `QUAST`
4. Viral contig identification with `VirSorter2`
5. Completeness and contamination assessment with `CheckV`
6. Tool version capture for provenance
7. Automated HTML and PDF reporting through R Markdown and WeasyPrint

Key strengths:

- End-to-end assembly plus biological QC in one run
- Automatic database handling for VirSorter2 and CheckV
- Unified summary output for multi-sample projects
- Built-in low-coverage flagging in the final report

---

## Pipeline 2: Nanopore Phage Assembly and QC

Repository: [phage-nanopore-assembly-snakemake](https://gitlab.ilvo.be/stevebaeyen/phage-nanopore-assembly-snakemake)

This workflow is optimized for Oxford Nanopore long reads and includes:

1. Raw read QC with `NanoPlot`
2. Read filtering with `Filtlong`
3. Adapter trimming with `Porechop_ABI`
4. Post-filter QC with `NanoPlot`
5. Long-read assembly with `Flye`
6. Assembly graph visualization with `Bandage`
7. Consensus polishing with `Medaka`
8. Viral identification with `VirSorter2`
9. Completeness and contamination profiling with `CheckV`
10. Assembly metrics with `QUAST`
11. Integrated HTML and PDF report generation

Key strengths:

- Long-read native assembly strategy
- Explicit assembly graph output for structural interpretation
- Configurable Medaka model support for modern ONT chemistries
- Robust handling of no-hit viral classification cases

---

## One Philosophy, Two Data Types

Although each pipeline is tuned for a different sequencing technology, both follow the same design philosophy:

- Modular, readable Snakemake rules
- Deterministic directory structure and outputs
- Automated dependency management with conda
- Traceable software versions
- Practical reports that summarize QC and biological relevance

This makes it easier to compare results across projects, collaborate between teams, and maintain consistent analytical standards.

---

## Typical Usage

Illumina workflow (example):

```bash
snakemake --use-conda --cores 24 --configfile config.yaml
```

Nanopore workflow (example):

```bash
snakemake --use-conda -j 24 --configfile config.yaml
```

In both workflows, a dry run is recommended before the first execution:

```bash
snakemake -n -p --use-conda --configfile config.yaml
```

---

## Final Thoughts

Reliable phage genomics depends on more than assembly alone. It requires clear quality metrics, transparent methods, and workflows that remain reproducible as projects scale.

These two pipelines were built to support that standard in day-to-day research: from raw reads to actionable, documented results.

If you work on phage genomics with Illumina or Nanopore data, I hope these workflows help you move faster with more confidence.

## Repositories

- Nanopore pipeline: [https://gitlab.ilvo.be/stevebaeyen/phage-nanopore-assembly-snakemake](https://gitlab.ilvo.be/stevebaeyen/phage-nanopore-assembly-snakemake)
- Illumina pipeline: [https://gitlab.ilvo.be/stevebaeyen/phage_assembly_snakemake](https://gitlab.ilvo.be/stevebaeyen/phage_assembly_snakemake)
