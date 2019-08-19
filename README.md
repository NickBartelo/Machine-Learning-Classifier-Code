# Machine-Learning-Classifier-Code
Code for manipulating files so that they can be used in a random forests model

The following describes the procedure used to sculpt data so that an accurate classifier
using a Random Forests Model can be created.

The bed file I was given was created by Matt Neville in Dr. Sudhakaran Prabakaran's lab and is entitled noinFrame_38.bed. 
It contains about 194,000 nORFs that we will use to map variants to from ClinVar, the HGMD database, and an Ensembl Enredo, Pecan, Ortheus Pipeline (EPO) 6-way Primate alignment, which I will refer to as the Human Derived SNVs and human derived. 

## Layout of Material

To follow the correct order of the pipeline, read the files in the following sequence:
1. Human Derived EPO 6-Way VCF file using liftover for hg19 to hg38 with Data Manipulation for LiftOver and Bedtools Intersect.ipynb
2. Bedtools Intersect with ClinVar, HGMD, and Human Derived with Data Manipulation for LiftOver and Bedtools Intersect.ipynb
3. CADD Annotations with CADD Annotations.ipynb
4. ANNOVAR Annotations with ANNOVAR Annotation.ipynb
5. Merging Annotations with Merging Annotations.ipynb
6. PCA for Variants.ipynb

Both the coding file and the Jupyter Notebook file must be looked at in order to achieve the correct results. It is suggested to have both files open side by side to look at which steps correspond with each line in the writing files. 

## Mapping Variants to nORFs

1. **Human Derived SNVs**

This file is the humanDerivedSNVs.vcf file. It has 5 columns. In order to be used for bedtools intersect, it must have 8 columns. Therefore, we read the file into Jupyter Notebook using the read_vcf() function found in this file. It is not necessary to have all 8 columns for liftover, but it must be done for bedtools intersect. How to do this can be found in the Jupyter Notebook file. The extra 3 columns in the human derived dataframe will be dummy columns, and are just made up of '.' The code to make a dataframe into a vcf using Python is executed and then we can use command line for the liftover from hg19 to hg38, first changing the vcf into a .bed file. The next step is to convert the hg38 .bed file back into a vcf using command line. Finally, we cut off the header using a text editor and read this file into Python.

* At this point, the dataframes we have are ready to be mapped to nORFs using the noinFrame_38.bed file. 

2. **Bedtools Intersect**

In order to run Bedtools Intersect on any of the files, the syntax for chromosome values must be the same for the files being used. It is shown how to do this in the file 'Bedtools Intersect with ClinVar, HGMD, and Human Derived' Also, the header of the .vcf files must be cut out, leaving only the data. The commands for bedtools intersect are fairly straightforward after these things have been taken into consideration.

* At this point, the dataframes contain the desired variants in nORFs only. Therefore, we can begin to annotate.

3. **CADD Annotations**

There are a few specifications by the CADD website that need to be taken into consideration before putting the files in for annotation, found below:
* Input files must be in VCF format
* Input files must be under 2 MB
* Input files must have less than 100,000 variants

It is necessary to cut out columns of the dataframes which are not needed for CADD Annotations. This saves a lot of space and allows for more variants to be processed per upload.
While reading the CADD Annotation files, there will be a clear guide on how to get passed these challenges.

* At this point, we have dataframes for variants mapped to nORFs which contain annotations from CADD.

4. **ANNOVAR Annotations**

It is necessary to have over 100 GB of space for the ANNOVAR annotations. The ANNOVAR Annotations document shows clearly how to download ANNOVAR from online for academic use and the steps needed to annotate the files correctly.
I manipulated the dataframes prepared for CADD annotations (with only 5 columns) to be used in ANNOVAR. The Jupyter Notebook file shows the necessary manipulations both before and after the annotations.

* At this point, we have dataframes for variants mapped to nORFs which contain annotations from ANNOVAR.


5. **Merging Annotations**

The Jupyter Notebook file contains all the information necessary for this part of the data manipulation and I would recommend only looking at this file. Merging annotations raises many issues with performance since the files are very large. It is necessary to begin using dask dataframe and considering other things, such as data types (dtypes). The Jupyter Notebook file shows the necessary pipeline to receive final dataframes for the pathogenic coding region, pathogenic noncoding region, benign coding region, and benign noncoding region which will be used for the classifier. 

* At this point, we have dataframes for variants mapped to nORFs which contain annotations from CADD and ANNOVAR. 

The next step is to understand how the annotations influence the model by using PCA correlation testing. By doing this, it is possible to cut out unnecessary columns from out dataframe, allowing the classifier to learn using significant data only.

6. **Pearson Correlation Tables and PCA**

In order to understand how our data will influence the model, we look at graphs of the correlation between annotations and we perform a PCA on the variants. The correlation graphs show how much the annotations influence the outcome of the model. PCA groups the variants based on common characteristics, revealing how the different sections of data (coding benign, noncoding benign, coding pathogenic, noncoding pathogenic) look. From this, we may be able to make additional inferences on our data if the regions are found to be grouped separately. 

# References

Wang K, Li M, Hakonarson H. ANNOVAR: Functional annotation of genetic variants from next-generation sequencing data Nucleic Acids Research, 38:e164, 2010

Rentzsch P, Witten D, Cooper GM, Shendure J, Kircher M. 
CADD: predicting the deleteriousness of variants throughout the human genome.
Nucleic Acids Res. 2018 Oct 29. doi: 10.1093/nar/gky1016.
PubMed PMID: 30371827.
