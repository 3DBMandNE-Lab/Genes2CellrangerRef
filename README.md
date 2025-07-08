# Genes2CellrangerRef
A small tutorial and code snippets about how to add Custom genes to the cellranger reference.

### 1. Copy the "custom" folder into you Cellranger folder

### 2. Search for your (human) refernence file folder. named eg **"refdata-gex-GRCh38-2024-A"**

### 3. There are two subfolders you are intrested in: **"genes"** and **"fasta"**

### 4. Copy the refdata-gex-GRCh38-2024-A/ fasta / genome.fa into: Cellranger/ custom/ fasta

### 5. Copy the refdata-gex-GRCh38-2024-A/ genes / genes.gtf into: Cellranger/ custom/ genes

     Leave these two files untouched until concatinating the new gene info into them. They are very large and you will most likely not be able to open them with the normal text edit software, but we can concat. new lines (new gene info) into these big files. 

### 6. In the folders  Cellranger/ custom/ fasta and genes, there are already files called: custom_transgenes.fa and custom_transgenes.gtf. 

### 7. Lets start with custom_transgenes.fa in the fasta folder: 

- Open the file via: 
```bash
cd  ~/Cellranger/custom/fasta
nano custom_transgenes.fa
```

- the format will be like this: 
```
>ZsGreen1
ATGGCCCAGTCC...
>ZsYellow1
ATGGCCCACAGC...
```

-> Here : you need to mention your gene name exactly how you will want it to show up later and copy the full nucleotide sequence right below it. 
- Take a note of the number of nucleotides it is long you will need it later.

eg. Easy way in python:
``` python
 len("ATGGCCCACAGC...")
```
- remove every other entry which is not new. (To avoid repeats)

- write out the file by pressing "ctrl + O", press enter, and close nano with "ctrl + X"

### 8. Now onto the custom_transgenes.gtf in the "genes" folder. 


- Open the file via: 
```bash
cd  ~/Cellranger/custom/genes
nano custom_transgenes.gtf
```

- the format will be like this: 

```
ZsGreen1	custom	exon	1	696	.	+	.	gene_id "ZSGREEN1"; gene_version "1"; transcript_id "ZSGREEN1-001"; transcript_version "1"; gene_type "protein_coding"; gene_name "ZsGreen1"; transcript_type "protein_coding"; transcript_name "ZsGreen1-001"; exon_number 1; exon_id "ZSGREEN1-E001"; exon_version "1"; level 1; tag "manual_annotation";

mCherry	custom	exon	1	711	.	+	.	gene_id "MCHERRY"; gene_version "1"; transcript_id "MCHERRY-001"; transcript_version "1"; gene_type "protein_coding"; gene_name "mCherry"; transcript_type "protein_coding"; transcript_name "mCherry-001"; exon_number 1; exon_id "MCHERRY-E001"; exon_version "1"; level 1; tag "manual_annotation";

...
```
These entries are space seperated. so take care to only have single spaces between each entry. 

- The inial entry is the gene name. **It has to be** the same one you used in the **custom_transgenes.fa** file. **This is important!**

- The second entry is the "chromosome" here we add "custom" to not get cell ranger confused. Basicly stating this gene is found on a custom chromosome. 

- "Exon" stays as it, as well as the "1".

- The second thing we need to adjust is the Gene length. Here we enter the number we got in step 7. **This is important!**

- You can now adjust some other variables in this entry for your custom gene. Like other times the gene name is mentioned. Just replace them with your custom gene name. 

- remove every other entry which is not new. (To avoid repeats)

### 9. Now we are ready to concatinate. Stay in the genes folder (in terminal) and: 

```bash 
# ~/Cellranger/custom/genes
cat custom_transgenes.gtf >> genes.gtf
```

### 10. Go in to the fasta folder and: 

```bash 
# ~/Cellranger/custom/fasta
cat custom_transgenes.fa >> genome.fa
```

### 11. Let cellranger stitch things together 

- Go into the main Cellranger folder and run: 

```bash
./cellranger mkref   --genome=GRCh38_with_transgenes   --fasta=custom/fasta/genome.fa   --genes=custom/genes/genes.gtf

```

### 12. If you did everything right you are DONE!!!

- common mistakes are: 
    - too many spaces between the entries in custom_genes.gtf
    - wrong gene length mentioned in custom_genes.gtf
    - missmatch in the gene name used between the two files

