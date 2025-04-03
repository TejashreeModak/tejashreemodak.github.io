---
layout: post
title: Use of Python Set Operations 
date: 2025-04-03
---

## Python tool for gene list comparisons

As a scientist analyzing NGS data, one of the common bioinformatics task I need to use is comparing datasets to find common and unique features. One exampleis comparing gene lists in different samples or treatments or calls in NGS runs. There are many methods one can use, owing to preference in scripting language or favorite packages. 

I was recently analyzing scRNAseq data to identify EMT biomarkers in breast cancer samples. We were using mouse models and at one point in my analysis I wanted to compare EMT hallmark genes against my dataset. I used the [MsigDB](https://www.gsea-msigdb.org/gsea/msigdb/human/geneset/HALLMARK_EPITHELIAL_MESENCHYMAL_TRANSITION.html) as a resource to get the hallmark genes. I also wanted to compare hallmark gene lists between mouse and human.  

Here is a simple *python script* that uses *set operations* to do just that. 


```python

from pprint import pprint

# Read and process human gene list
with open('EMT_Human_Hallmark_genelist.txt', 'r') as f:
    human = {line.strip() for line in f if line.strip() and line.strip() != "GENE_SYMBOLS"}

# Read and process mouse gene list
with open('EMT_Mouse_Hallmark_genelist.txt', 'r') as f:
    mouse = {line.strip().upper() for line in f if line.strip() and line.strip() != "GENE_SYMBOLS"}

# Compute intersections and differences
common_genes = human & mouse
human_only = human - mouse
mouse_only = mouse - human

# Print results with formatting
print("\n=== Common Genes in Human and Mouse ===")
pprint(common_genes, compact=True)

print(f"\nTotal Number of Common Genes: {len(common_genes)}")

print("\n=== Human Genes Not in Mouse ===")
pprint(human_only, compact=True)

print("\n=== Mouse Genes Not in Human ===")
pprint(mouse_only, compact=True)

```

This python script uses the following to note for new users: 
- Used with open(...) as f: Ensures files are properly closed after reading.
- Used set comprehensions: Cleaner and more Pythonic way to filter and store gene names.
- Used pprint.pprint(): Pretty-prints sets, making the output more readable.
- Added section headers and formatting: Makes the output structured and easier to interpret.


Since I find myself making such comparisons in many different scenarios or projects why not make a tool out of this script that can do these comparisons for any two files with gene names that the user inputs.

```python

import argparse
from pprint import pprint

def read_gene_list(file_path, convert_upper=False):
    """Reads a gene list from a file, removes empty lines and headers."""
    with open(file_path, 'r') as f:
        genes = {line.strip().upper() if convert_upper else line.strip() for line in f if line.strip() and line.strip() != "GENE_SYMBOLS"}
    return genes

def compare_gene_lists(file1, file2, convert_upper=True):
    """Compares two gene lists and prints common and unique genes."""
    genes1 = read_gene_list(file1, convert_upper)
    genes2 = read_gene_list(file2, convert_upper)
    
    common_genes = genes1 & genes2
    unique_to_1 = genes1 - genes2
    unique_to_2 = genes2 - genes1
    
    print(f"\n=== Common Genes in {file1} and {file2} ===")
    pprint(common_genes, compact=True)
    print(f"\nTotal Number of Common Genes: {len(common_genes)}")
    
    print(f"\n=== Genes in {file1} but Not in {file2} ===")
    pprint(unique_to_1, compact=True)
    
    print(f"\n=== Genes in {file2} but Not in {file1} ===")
    pprint(unique_to_2, compact=True)

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Compare two gene lists and identify common and unique genes.")
    parser.add_argument("file1", help="Path to the first gene list file")
    parser.add_argument("file2", help="Path to the second gene list file")
    args = parser.parse_args()
    
    compare_gene_lists(args.file1, args.file2)

```

Here's the full script as a *standalone tool*. It takes two gene list files as input via command-line arguments, compares them, and prints common and unique genes in a readable format.

Although I have used gene names as an example, this script can also be used for any other data like sample names in two treatments, variant calls in two samples etc. Any two txt, csv or tsv with one name per line can be compared to another using this script. It can also be made more generic by adding filetypes or additional comparisons. I like to keep these tool scripts simple and generic to allow use for many different scenarios. As I use it, I might think of new features or additional scripts for daily tasks. 

These habits make me a more effecient programmer and scientist with reproducible and transparent science. 

