# Machine-Learning-Classifier-Code
Code for manipulating files so that they can be used in a random forests model

The following describes the procedure used to sculpt data so that an accurate classifier
using a Random Forests Model can be created.

The bed file I was given was created by Matt Neville in Dr. Sudhakaran Prabakaran's lab and is entitled noinFrame_38.bed. 
It contains about 194,000 nORFs that we will use to map variants to from ClinVar, the HGMD database, and an Ensembl Enredo, Pecan, Ortheus Pipeline (EPO) 6-way Primate alignment, which I will refer to as the Human Derived SNVs.

## Layout of Material

To follow the correct order of the pipeline, read the files in the following sequence:
1. Human Derived EPO 6-Way VCF file using liftover for hg19 to hg38 with Data Manipulation for LiftOver and Bedtools Intersect.ipynb
2. Bedtools Intersect with ClinVar, HGMD, and Human Derived with Data Manipulation for LiftOver and Bedtools Intersect.ipynb
3. CADD Annotations with CADD Annotations.ipynb
4. ANNOVAR Annotations with ANNOVAR Annotation.ipynb
5. Merging Annotations with Merging Annotations.ipynb
6. 

## Mapping Variants to nORFs

1. Human Derived SNVs 

This file is the humanDerivedSNVs.vcf file. It has 5 columns. In order to be used for bedtools intersect, it must have 8 columns. Therefore, we read the file into Jupyter Notebook using the read_vcf() function found in this file. It is not necessary to have all 8 columns for liftover, but it must be done for bedtools intersect. How to do this can be found in the Jupyter Notebook file. The extra 3 columns in the human derived dataframe will be dummy columns, and are just made up of '.' The code to make a dataframe into a vcf using Python is executed and then we can use command line for the liftover from hg19 to hg38, first changing the vcf into a .bed file. The next step is to convert the hg38 .bed file back into a vcf using command line. Finally, we cut off the header using a text editor and read this file into Python.



# References

Wang K, Li M, Hakonarson H. ANNOVAR: Functional annotation of genetic variants from next-generation sequencing data Nucleic Acids Research, 38:e164, 2010

Rentzsch P, Witten D, Cooper GM, Shendure J, Kircher M. 
CADD: predicting the deleteriousness of variants throughout the human genome.
Nucleic Acids Res. 2018 Oct 29. doi: 10.1093/nar/gky1016.
PubMed PMID: 30371827.
