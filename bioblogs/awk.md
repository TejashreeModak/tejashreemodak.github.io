---
layout: post
title: AWK 
date: 2025-03-17
---

## Bioinformatics sidekicks: AWK tips and tricks

Most people focus on proficiency in R/Python or other programming languages for bioinformatics work. Although these are critical superheroes for bioinformatics/computational biology work there are some very powerful sidekicks that work their magic in some basic tasks all the time.

Awk is one such wonderful tool that can help do quick data filtering or format transformation very fast.
Here are some tricks I used over the years.

You can quickly extract specific fields for further downstream analysis or generating input files for tools who need only few of the fields from a large data set. To do this extraction though you need to know how many fields exist within your file. You can quickly do this right on the commandline with awk!

### Count number of columns in a tab separated file:

```bash
`awk -F'\t' '{print NF; exit}' file.tsv
```
- **`awk -F'\t'` Sets the field separator to a tab (\t).
- **`'{print NF; exit}'` NF (Number of Fields) represents the total columns in a row. exit ensures it only processes the first line, making it efficient.

Now you are ready to pull specific fields that interest you.

### Print selected columns in a tab delimited file:
```bash
awk FS='\t' '{print $1, $5, $6}' file.tsv
```

### Choose order of columns to print in a tab delimited file:
They need not be in order. Sometimes we need to reorder fields as we extract and awk can easily do that for you!

```bash
awk FS='\t' '{print $5, $1, $6}' file.tsv
```

### Print last two columns of a tab separated file:
Sometimes you need to get last few fields and don’t care to find out how many fields exist in the file.

```bash
awk '{print $(NF-1),"\t",$NF}' file.tsv
```
### Print last and 3rd last column
```bash
awk '{print $(NF-2),"\t",$NF}' file.tsv
```
### To combine fields with a separator and print rest as is
Sometimes you need to extract fields and combine them as a single field with a separator. You can write a small R/python script but awk will let you do that in seconds on the commandline itself so you can jump into your next step instantly.

```bash
awk -F'\t' '{print $1 ":" $3 "\t" $2}' file.tsv
```

### Tab delimited to FASTA file
You can also make fasta file from a delimited file with awk. Lets say your fwd and rev primers for a gene target and their sequences are stored in separate columns of a delimited file along with other information in rest of the columns:

```bash
awk -F',' 'NR>1 {print ">" $2 "_fwd"; print $10; print ">" $2 "_rev"; print $17}' file.csv > fasta.fa
```

### Filtering files
Lets say you are using a GTF file and are interested in a region on chr17 only. Look at lines corresponding to a particular chr in a gtf file:

```bash
zgrep -v '^#' Homo_sapiens.GRCh37.87.gtf.gz| awk -F'\t' '$1 == "17"' | less
```
### **Pro tip:** 
- **Specify your separator to awk using `‘-F’`. 
- **If you want a different separator in your output file than in the input file define it using `‘-OFS’`.
- **To read in a tab separated file and output a comma separated output: `awk -F'\t' -OFS=',' '{print $1, $2, $3}' file.tsv` 


