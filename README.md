# 🧬 VGP Genome Assembly on Galaxy
**Course Assignment** | *Vertebrate Genome Project (VGP) Pipeline using UseGalaxy.org*

Based on the [Galaxy Training Network](https://training.galaxyproject.org/) tutorial.

---

## 🧭 Project Overview
This repository documents the implementation of the **Vertebrate Genomes Project (VGP)** pipeline. The goal is to generate high-quality, chromosome-level, haplotype-phased genome assemblies.

### 📊 Data Sources
* **PacBio HiFi Reads:** Long, highly accurate reads (~10–20 kbp) for contig assembly.
* **Hi-C Reads:** Chromatin interaction data used for chromosome-scale scaffolding.

---

## 🗺️ Pipeline Overview
The pipeline moves from raw reads to a chromosome-scale assembly through four main stages:

1. **Genome Profiling** → Estimate size, heterozygosity, and repeat content.
2. **Contig Assembly** → Assemble HiFi reads into phased haplotype contigs.
3. **Post-Assembly QC** → Remove duplicates and assess completeness.
4. **Hi-C Scaffolding** → Order and orient contigs into final chromosomes.

---

## 🔬 Step-by-Step Walkthrough

### Step 1: Data Upload
We uploaded HiFi reads (`.fastq.gz`) and Hi-C reads (`.bam`) to the Galaxy history.

### Step 2: Genome Profiling (K-mer Analysis)
**Tools:** `Meryl` + `GenomeScope2`  
We used $k=31$ to analyze the genome properties before assembly.

| Parameter | What it tells us |
| :--- | :--- |
| **Genome size** | Total length (used to parameterize downstream steps). |
| **Heterozygosity** | How different the two haplotype copies are. |
| **Repeat content** | Percentage of the genome that is repetitive. |
| **Error rate** | The quality/cleanliness of the raw reads. |

---

### Step 3: Contig Assembly with `hifiasm`
**Tool:** `hifiasm` (Hi-C phasing mode)  
Hifiasm builds an overlap graph and resolves haplotype "bubbles" using Hi-C data.

**Assembly Statistics (via `gfastats`):**
| Metric | Meaning |
| :--- | :--- |
| **No. of contigs** | Total pieces — fewer is better. |
| **Total length** | Should match the estimated genome size. |
| **N50** | Half the assembly is in contigs this long or longer. |
| **L50** | Number of contigs needed to reach the 50% mark. |

---

### Step 4: Purging Duplicate Sequences
**Tool:** `purge_dups`  
This tool identifies "haplotigs" (duplicated regions from both haplotypes in one assembly) based on read coverage and moves them to the alternate assembly.

---

### Step 5: Assembly Quality Control
**Tools:** `BUSCO`, `Merqury`

* **BUSCO:** Measures completeness based on conserved genes.
* **Merqury:** Reference-free assessment using k-mers to find the **QV (Quality Value)**.

---

### Step 6: Hi-C Scaffolding
**Tools:** `BWA-MEM`, `Samtools`, `PretextMap`  
We mapped Hi-C reads to the purged assembly to visualize the 3D proximity of contigs.

* **PretextMap:** Generates a heatmap. Bright squares along the diagonal represent contigs that belong to the same chromosome.

---

## 🔑 Key Concepts

| Term | Plain English |
| :--- | :--- |
| **Contig** | A continuous piece of DNA with no gaps. |
| **Scaffold** | Contigs joined in order with gaps (Ns) between them. |
| **Haplotype** | One of the two copies of each chromosome (maternal/paternal). |
| **Hi-C** | A technique capturing 3D genome organization. |

---
> **Note:** This project was completed using the [UseGalaxy.org](https://usegalaxy.org) platform.
