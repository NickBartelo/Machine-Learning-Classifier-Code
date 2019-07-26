# Machine-Learning-Classifier-Code
Code for manipulating files so that they can be used in a random forests model

The following describes the procedure needed to be used to sculpt data so that it can be used to create a classifier 
using a Random Forests Model

The bed file I was given was created by Matt Neville in Dr. Sudhakaran Prabakaran's lab and is entitled noinFrame_38.bed. 
It contains about 194,000 nORFs that we will use to map variants to from ClinVar, the HGMD database, and an Ensembl EPO 6-way 
Primate alignment, which I will refer to as the Human Derived SNVs.

## Mapping Variants to nORFs

1. Human Derived SNVs 

This file is the humanDerivedSNVs.vcf file. It has 5 columns. In order to be used for bedtools intersect, it must have 8 columns. Therefore, we read the file into Jupyter Notebook using the read_vcf() function found in this file. It is not necessary to have all 8 columns for liftover, but it must be done for bedtools intersect. How to do this can be found in the Jupyter Notebook file. The extra 3 columns in the human derived dataframe will be dummy columns, and are just made up of '.' The code to make a dataframe into a vcf in Python is executed and then we can use command line for the liftover from hg19 to hg38. 
