# Genes2CellrangerRef

A guide and resource for adding custom genes (transgenes) to a Cell Ranger reference genome. This repository provides example files, directory structure, and step-by-step instructions for integrating new gene sequences into your Cell Ranger workflow.

---

## Project Structure

```
custom/
  fasta/
    custom_transgenes.fa         # FASTA file with custom gene sequences
    genome.fa.fai                # FASTA index (from reference)
  genes/
    custom_transgenes.gtf        # GTF file with custom gene annotations
  individual gene sequences/
    AAV2 ITR.txt, EGFP_AAV.txt, ...  # Individual gene sequence files
README.md
```

---

## Overview

This tutorial explains how to add custom genes (e.g., transgenes, reporters) to an existing Cell Ranger reference. You will:
- Copy your reference genome and annotation files
- Add your custom gene sequences and annotations
- Concatenate them to the reference files
- Build a new reference with Cell Ranger

---

## Step-by-Step Instructions

1. Copy the `custom` folder into your Cell Ranger folder.
2. Locate your reference folder (e.g., `refdata-gex-GRCh38-2024-A`)
3. Identify the `genes` and `fasta` subfolders in the reference
4. Copy the reference files:
- `refdata-gex-GRCh38-2024-A/fasta/genome.fa` → `Cellranger/custom/fasta/`
- `refdata-gex-GRCh38-2024-A/genes/genes.gtf` → `Cellranger/custom/genes/`

> **Note:** Leave these files untouched until you concatenate the new gene info. They are large and may not open in standard text editors.

5. Prepare your custom gene files
- `custom/fasta/custom_transgenes.fa`: Add your custom gene sequences in FASTA format.
- `custom/genes/custom_transgenes.gtf`: Add your custom gene annotations in GTF format.

#### Example `custom_transgenes.fa` format:
```
>ZsGreen1
ATGGCCCAGTCC...
>ZsYellow1
ATGGCCCACAGC...
```
- The header (`>GeneName`) must match the gene name used in the GTF file.
- The sequence is the full nucleotide sequence.
- Remove any non-new entries to avoid duplicates.

#### Example `custom_transgenes.gtf` format:
```
ZsGreen1	custom	exon	1	696	.	+	.	gene_id "ZSGREEN1"; gene_version "1"; transcript_id "ZSGREEN1-001"; ...
```
- Fields are tab-separated. Use single spaces within the attributes field.
- The gene name must match the FASTA entry.
- The second column is `custom` (chromosome name).
- The exon length (columns 4-5) must match the sequence length.
- Adjust other fields as needed for your gene.

6. Concatenate custom entries to the reference files

In the `genes` folder:
```bash
cat custom_transgenes.gtf >> genes.gtf
```

In the `fasta` folder:
```bash
cat custom_transgenes.fa >> genome.fa
```

7. Build the new reference with Cell Ranger

From the main Cell Ranger folder:
```bash
./cellranger mkref \
  --genome=GRCh38_with_transgenes \
  --fasta=custom/fasta/genome.fa \
  --genes=custom/genes/genes.gtf
```

---

## Troubleshooting & Tips

- Ensure gene names match exactly between FASTA and GTF files.
- The exon length in the GTF must match the nucleotide count in the FASTA.
- Use only single spaces between attributes in the GTF.
- Common mistakes:
  - Extra spaces or tabs in GTF
  - Incorrect gene length
  - Name mismatches
- Use command-line tools (e.g., `wc`, `awk`, `grep`) to check file formats and lengths.


