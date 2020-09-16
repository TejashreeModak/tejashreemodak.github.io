
---
layout: post
title: FASTA tools 
date: 2020-09-10
---

## How to rename headers in FASTA files 

FASTA format is a commonly used format that is required in a multitude of applications and tools to be used. I often find the need to manipulate them in various ways to suit the task at hand. Once such requirement is to change the headers that are unique identifiers of the sequences. Here is a simple script that does just that. 

## A text file with old and new names

All you need is a txt file that holds old names in field 1 and new names in field 2 separated by a comma as stored as a csv file. You can get the old names from a FASTA file by using `grep '^>' <file.fasta>`. Create a new name field as you want in the edited FASTA. Here is what it should look like: 

```shell
old_name,new_name
Sample1,Sample1_2020
Sample2,Sample2B
Sample3,Control1
```
Lets say you want to add the year of collection to Sample1, add a subsample name B to Sample2 and label Sample3 as your control. Your new name can be anything you want to change to. Make sure the old name is exactly as in the original FASTA file. 

## Create script to rename headers

Below is the script that you can save as a .py file. 

```python
import sys
import argparse
import csv

parser = argparse.ArgumentParser()
parser.add_argument("--mapping-file", metavar="F", type=argparse.FileType("r"), required=True,
                    help="A csv mapping file containing 2 columns - 'old-name,new-name'")
parser.add_argument("-i", type=argparse.FileType("r"), default=sys.stdin,
                    help="The input fasta file (default: stdin)")
parser.add_argument("-o", type=argparse.FileType("w"), default=sys.stdout,
                    help="The output fasta file (default: stdout)")

args = parser.parse_args()

# args.mapping_file is a csv with 2 columnes 'old-name,new-name' 
# The <old-name> in input fasta will be replaced by <new-name>_<old_name>
mapping = {}
for old_name, new_name in csv.reader(args.mapping_file, dialect=csv.excel):
    mapping[old_name] = new_name

for line in args.i:
    if line.startswith(">"):
        name = line[1:].strip()
        if name in mapping:
            line = '>' + mapping[name] + '\n'
    args.o.write(line)

```

## How to run the script

Once you save the script lets say as fasta_rename.py and you have the csv file ready where you have the old and mew names you are ready to use this script to change the headers.

```shell
python rename_fasta.py -h

```

Using -h will invoke help for the script. This will tell you how to enter the arguments. If you keep the script as is here is how you can run it.

```shell
python rename_fasta.py --mapping-file <path-to-csv-file> \
        -i <path-to-original-FASTA-file> -o <path-to-edited-FASTA-file> 
```

This will create a new FASTA file with edited header names in the location specified! 
