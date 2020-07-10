
---
layout: post
title: VCFTools
date: 2020-07-10
---

## How to subset or filter individuals from a vcf file.

A vcf file format is a very complicated format and manually editing the file is really not a good idea. Often I find myself in a situation where I want analyze a subgroup of samples separately or create separate groups of samples that are in a single vcf file. VCFTools has a very useful functionality that can allow the user to choose which individuals to remove/keep in a vcf file while maintaining the format.

If you want to keep a single or a just a few individuals you can specify them directly on commandline after --indiv option. 

`vcftools --vcf <original vcf file> --indiv <indiv name> --recode --recode-INFO-all --out <new vcf file>`

The --recode option generates a new vcf file with the changes. If you want a bcf file instead you can use --recode-bcf to output a bcf file. --recode-INFO-all option is used to keep all the information in the original vcf exactly the same in the outputvcf which is most often the requirement since the goal is to subset only. --out specifies the prefix for the outfile. By default VCFTools adds a suffix of ".recode.vcf" to the prefix specified by the user. 

You can also provide a list of individuals to keep from the original vcf file by making a text file with one name per line and using the --keep option.

`vcftools --vcf <original vcf file> --keep <file with indiv names> --recode --recode-INFO-all --out <new vcf file>`

If you want to filter out an individual you can use

`vcftools --vcf <original vcf file> --remove-indiv <indiv name> --recode --recode-INFO-all --out <new vcf file>`

or if you want a list of individuals removed use

`vcftools --vcf <original vcf file> --remove <file with indiv names> --recode --recode-INFO-all --out <new vcf file>`

An important thing to rememner is that the individual names have to exactly match the ones in the header of the original vcf file.  
