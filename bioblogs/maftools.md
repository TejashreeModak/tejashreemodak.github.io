---
layout: post
title: Genomics Visualization in R 
date: 2025-04-09
---

## Visualizing Mutation Landscapes from WES/WGS Data Using R

Visualization is a very important aspect of working with omics data. It is not only handy in presentations but can provide an overview of the data you are working with. Especially when working with large amounts of data as is everyday in omics analysis. 

Over the years, I have used several packages for visualization in -omics data in R. Here I have chosen one that can provide an overview of variants in a **genomics** data set.

Visualizing mutation landscapes from whole-exome (WES) or whole-genome sequencing (WGS) is a powerful way to understand the genetic alterations driving disease, especially in cancer. One of the most popular tools in R for summarizing and visualizing such data is [`maftools`](https://bioconductor.org/packages/release/bioc/html/maftools.html).

In this post, we'll walk through how to generate **oncoplots** (a grid-based mutation summary across patients) using a mutation annotation file (MAF) with just a few lines of R code.


### Prerequisites

We'll be using the `maftools` package, which provides functions to read, analyze, and visualize MAF files.

```r
# Install Bioconductor if needed
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

# Install maftools
BiocManager::install("maftools")

```
Now load the necessary packages. 

```r
library(maftools)
library(ggplot2)  # optional, for custom plotting if needed
```

### Example data

`maftools` comes with example data that mimics a real-world cancer mutation dataset. For this tutorial, we'll use the laml dataset (Acute Myeloid Leukemia from TCGA):

```r
# Load example AML dataset
laml <- system.file("extdata", "tcga_laml.maf.gz", package = "maftools")
clinical <- system.file("extdata", "tcga_laml_annot.tsv", package = "maftools")

# Read the MAF file
laml_maf <- read.maf(maf = laml, clinicalData = clinical)

# Quick summary
laml_maf

```

### Oncoplot: Visualizing Mutation Landscape

The oncoplot is a great way to visualize the top mutated genes across multiple samples. Here is the example from the manual. 

```r
# Plot the top 10 mutated genes
oncoplot(maf = laml_maf, top = 10)
```
We can customize this plot to focus on aspects of data important to you. We can also change colors to our liking. Below is the code to
- change colors
- focus on FAB classification
- classify variants into types of transitions and transversions 

```r
my_colors <- RColorBrewer::brewer.pal(8, "Set2")
oncoplot(
  maf = laml,
  top = 15,
  draw_titv = TRUE,
  sortByAnnotation = TRUE,
  clinicalFeatures = "FAB_classification",
  colors = my_colors,
  removeNonMutated = TRUE
)
```
Now the plot looks like this:

<img align="left" src="/img/FAB_oncoplot.png">

### Real-World Mutation Data: Breast Cancer (TCGA)

Now lets use some publicly available real-world data from [cBioPortal](https://www.cbioportal.org/) - specifically the Breast Invasive Carcinoma (BRCA) TCGA cohort. 

First go to [cBioPortal](https://www.cbioportal.org/study/summary?id=brca_tcga_pan_can_atlas_2018) and download the **Mutation data (MAF format)** for TCGA BRCA (PanCan Atlas). Save the `.maf` file locally.

Next, load and Explore Data in R.

```r
library(maftools)

# Load your downloaded MAF file
brca_maf <- read.maf(maf = "data_mutations_extended.maf")

# Quick overview
brca_maf
```
Now we can plot the summary and also generate the oncoplot like we did before for laml data for top genes.

```r
plotmafSummary(maf = brca_maf, dashboard = TRUE)
oncoplot(maf = brca_maf, top = 15)

```

We can also focus on one gene we are interested in and look at the mutation landscape for that specific gene. Lollipop plots are useful for this. 

```r
lollipopPlot(maf = brca_maf, gene = "TP53")
```

<img align="left" src="/img/Lollipop_plot_TP53.png">

## Summary

With just a few tweaks and real-world data, you can create beautiful, informative plots that:

- Summarize complex mutation data
- Highlight differences across subtypes or conditions
- Serve as powerful visual tools in presentations or manuscripts
