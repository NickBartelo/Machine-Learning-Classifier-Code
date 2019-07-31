# Machine-Learning-Classifier-Code
Code for manipulating files so that they can be used in a random forests model

The following describes the procedure needed to be used to sculpt data so that it can be used to create a classifier 
using a Random Forests Model

The bed file I was given was created by Matt Neville in Dr. Sudhakaran Prabakaran's lab and is entitled noinFrame_38.bed. 
It contains about 194,000 nORFs that we will use to map variants to from ClinVar, the HGMD database, and an Ensembl EPO 6-way 
Primate alignment, which I will refer to as the Human Derived SNVs.

## Layout of Material

To follow the correct order of the pipeline, read the files in the following sequence:
1. Human Derived EPO 6-Way VCF file using liftover for hg19 to hg38
2. Bedtools Intersect with ClinVar, HGMD, and Human Derived
3. CADD Annotations
4. ANNOVAR Annotations
5.

## Mapping Variants to nORFs

1. Human Derived SNVs 

This file is the humanDerivedSNVs.vcf file. It has 5 columns. In order to be used for bedtools intersect, it must have 8 columns. Therefore, we read the file into Jupyter Notebook using the read_vcf() function found in this file. It is not necessary to have all 8 columns for liftover, but it must be done for bedtools intersect. How to do this can be found in the Jupyter Notebook file. The extra 3 columns in the human derived dataframe will be dummy columns, and are just made up of '.' The code to make a dataframe into a vcf in Python is executed and then we can use command line for the liftover from hg19 to hg38. 



# References

Wang K, Li M, Hakonarson H. ANNOVAR: Functional annotation of genetic variants from next-generation sequencing data Nucleic Acids Research, 38:e164, 2010

Rentzsch P, Witten D, Cooper GM, Shendure J, Kircher M. 
CADD: predicting the deleteriousness of variants throughout the human genome.
Nucleic Acids Res. 2018 Oct 29. doi: 10.1093/nar/gky1016.
PubMed PMID: 30371827.
