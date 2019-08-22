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
6. Pearson Correlation Tables and PCA.ipynb
7. Random Forest NonCoding Region Classifier.ipynb and Random Forest Coding Region Classifier
8. Future Work

Both the coding file and the Jupyter Notebook file must be looked at in order to achieve the correct results. It is suggested to have both files open side by side to look at which steps correspond with each line in the writing files. 

#### Although some of the cells show errors, all cells in the notebooks must be called. These will not give errors when run straight through.

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
The CADD annotations files have different names than in the CADD Annotations.ipynb file, but are still the same annotated variants.

* At this point, we have dataframes for variants mapped to nORFs which contain annotations from ANNOVAR.


5. **Merging Annotations**

The Jupyter Notebook file contains all the information necessary for this part of the data manipulation and I would recommend only looking at this file. Merging annotations raises many issues with performance since the files are very large. It is necessary to begin using dask dataframe and considering other things, such as data types (dtypes). The Jupyter Notebook file shows the necessary pipeline to receive final dataframes for the pathogenic coding region, pathogenic noncoding region, benign coding region, and benign noncoding region which will be used for the classifier. Again, the names of some of the annotations from CADD are called in with different names than from the CADD Annotations.ipynb file. All files are present. For example, when calling in CADD annotated files for the human derived data, the GRCh38-v1.5_anno_0265e2d84faa1b92f64be0ad04982d58_noheader.tsv corresponds to the human_derived_noncoding_for_cadd_1_noheader.csv file. The same reasoning is used for all other CADD annotated files as well.

* At this point, we have dataframes for variants mapped to nORFs which contain annotations from CADD and ANNOVAR. 

The next step is to understand how the annotations influence the model by using PCA correlation testing. By doing this, it is possible to cut out unnecessary columns from out dataframe, allowing the classifier to learn using significant data only.

6. **Pearson Correlation Tables and PCA**

In order to understand how our data will influence the model, we look at graphs of the correlation between annotations and we perform a PCA on the variants. The correlation graphs show how much the annotations influence the outcome of the model. We see from our graphs that there are many annotations, in blue, which can be cut out of the model, reducing the dimensionality of the model. PCA groups the variants based on common characteristics, revealing how the different sections of data (coding benign, noncoding benign, coding pathogenic, noncoding pathogenic) look. From this, we may be able to make additional inferences on our data if the regions are found to be grouped separately. Unfortunately for our data, the regions seem to be extremely similar.

* At this point, we will begin the process of manipulating the data to be used in the models and finally run the models. To do so, we must have a lot of computation space and RAM (I used over 200 GB). 

7. **Random Forest NonCoding Region Classifier/Random Forest Coding Region Classifier** 

In Jupyter Notebook, open a python terminal and type:
pip install tornado==4.5.3 
The default version of tornado was not compatible with certain parts of the model so the older 4.5.3 version was needed. 

In addition, it is necessary to also type the following in the terminal:
jupyter notebook --NotebookApp.iopub_data_rate_limit=10000000
This allows more data to be processed by Jupyter Notebook. If the above line is not called, Jupyter Notebook may not allow some of our cells to be called.

Finally, it is necessary to enter the following code into the terminal as well:
echo 1 > /proc/sys/vm/overcommit_memory
This allows Python to ignore the usage of higher than normal computation (e.g. 32 GB).

In the interest of not having extremely large outputs be shown on github, which we receive from running some of the code in these notebooks, I cleared the output of some cells before downloading the notebooks to github, making the cells look as if they were not called. This being said, it is still necessary to call these cells. 

I recommend looking at the Random Forest NonCoding Region Classifier first because this notebook contains more than the other classifier. There was not enough time to go as in depth with the features for the Coding Region. However, each model gives an output of cross validation run on the testing sets, precision recall curves, and an analysis of the important features for the model. 

8. **Future Work** 

It is important to develop the two models above further and test these models on other data sets to see if the classifiers created have been overfit to the training set (and even the testing set) variants. In addition, we must compare this model to other well known classifiers, such as CADD and FATHMM, to find if our classifier is more efficient than the others. Taking the model one step further, we should next create the classifier using neural networks. This will help us truly understand the genetic mechanisms that drive our classifier. 

# References

Wang K, Li M, Hakonarson H. ANNOVAR: Functional annotation of genetic variants from next-generation sequencing data Nucleic Acids Research, 38:e164, 2010

Rentzsch P, Witten D, Cooper GM, Shendure J, Kircher M. 
CADD: predicting the deleteriousness of variants throughout the human genome.
Nucleic Acids Res. 2018 Oct 29. doi: 10.1093/nar/gky1016.
PubMed PMID: 30371827.
