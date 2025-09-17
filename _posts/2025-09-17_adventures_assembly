---
layout: post
title: "Adventures in Bacterial Genome Assembly with Snakemake"
date: 2025-09-17
categories: [bioinformatics, snakemake, gitlab]
tags: [assembly, bioinformatics, genomics]
giscus_comments: true
---

## 🧬 Adventures in Bacterial Genome Assembly: My Illumina Snakemake Pipelines on GitLab

Inspired by Dr. Ryan Wick’s and and Dr. Torsten Seeman's pioneering work in bacterial genome assembly, I developed a series of Snakemake workflows to streamline and scale genome assembly tasks. These pipelines are hosted on GitLab and designed for reproducibility, modularity, and ease of use.

### 🚀 Why Snakemake?

Snakemake enables reproducible and scalable workflows. It supports:

- Complex rule chaining for Illumina, Nanopore or hybrid data
- Integrated QC, taxonomic read and/or assembly classification, and polishing
- Version control via GitLab for transparency and collaboration
- Better resource management (jobs, CPU, monitoring)

### 🧪 The Pipelines

#### 1. Illumina Bacterial Assembly + QC

[GitLab Repository](https://gitlab.ilvo.be/stevebaeyen/illumina-bacterial-assembly-snakemake)

This pipeline assembles genomes from Illumina paired-end reads and includes QC tools like Quast, CheckM2, BUSCO, and taxonomic classification via Kraken2 and GTDB-Tk. Includes comprehensive HTML and PDF reports.

![dag](/assets/img/dag.svg)

#### 2. Assembly QC Pipeline

[GitLab Repository](https://gitlab.ilvo.be/genomics/wgs/bacterial-assembly-qc-snakemake)

Same QC tools as in the previous pipeline, but without assembly (useful for doing QC on downloaded assemblies). Generates summary plots (e.g., BUSCO, N50 beeswarm) and Excel reports for easy interpretation.

#### 3. Nanopore-only Pipeline

[Gitlab Repository](https://gitlab.ilvo.be/stevebaeyen/nanopore_only_snakemake)

## On my to-do list

- Updating my [Nanopore-only snakemake assembly and QC pipeline](https://gitlab.ilvo.be/stevebaeyen/nanopore_only_snakemake) to use [Autocycler](https://github.com/rrwick/Autocycler) instead of Flye assembly
- Adding HTML and PDF reporting to the Assembly QC and Nanopore-only snakemake pipelines.
- Replace skANI identification with Brooklin API for batch identification of assemblies against [Genomerxiv database](https://genomerxiv.cs.vt.edu/index.php).

## 🌱 Final Thoughts

These pipelines reflect a journey of technical growth and community engagement. Making a good assembly is an art, and evolves continuously with new insights and tools.

Explore the repositories, fork them, and let’s collaborate to advance microbial genomics—one Snakemake rule at a time.
