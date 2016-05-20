This tutorial was adapted from the [454 Overview Tutorial: de novo OTU picking and diversity analyses using 454 data](http://qiime.org/tutorials/tutorial.html)
Data for this tutorial is from the paper by *FAC Lopes et al*. [Microbial Community Profile and Water Quality in a Protected Area of the Caatinga Biome](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0148296)

###Learning Objectives
* Download raw data from NCBI's Sequence Read Archive (SRA)
* Understand file formats and conversion
* Generate a mapping file QIIME analysis

####Downloading the data from NCBI Sequence Read Archive (SRA)
The first step of data analysis is to access the data and the associated metadata. To publish the experiment and results in a research journal, the raw data is deposited in NCBI's SRA.

#####What is the SRA? (from [SRA Handbook](http://www.ncbi.nlm.nih.gov/books/NBK47528/))
The advent of massively parallel sequencing technologies has opened an extensive new vista of research possibilities — elucidation of the human microbiome, discovery of polymorphisms and mutations in individual genomes, mapping of protein–DNA interactions, and positioning of nucleosomes — to name just a few. *In order to achieve these research goals, researchers must be able to effectively store, access, and manipulate the enormous volume of short read data generated from massively parallel sequencing experiments.* In response to the research community’s need for such a resource, NCBI, EBI, and DDBJ, under the auspices of the International Nucleotide Sequence Database Collaboration (INSDC), have developed the Sequence Read Archive (SRA) data storage and retrieval system.

In the article by *FAC Lopes et al*, the sequences assessed in this study are available in [NCBI Sequence Read Archive (SRA)](http://www.ncbi.nlm.nih.gov/sra) under the study Accession Number PRJNA292014.

1. Click on the NCBI SRA link above. On the SRA website, find the search tool bar. Type in "PRJNA292014" and then click search.

2. The results page contain information about the BioProject (see image). Click the link 'Send results to Run Selector.'

3. The new page you're redirected contains information about the BioProject. Select all 12 samples using the green + icon below '12 Runs Found.' In the table above titled Download, click 'RunInfo Table.'

4. Once the file is downloaded, the text file can be opened in Excel/wordpad/text edit. It contains 'metadata' and sample information that is useful to look at. Here we want the 'Run_s' and 'Sample_Name_a' information.
```
Run_s	      Sample_Name_s
SRR2146911	Par_Nas_2012_1
SRR2146947	Par_Nas_2012_2
SRR2147711	Par_AnPN_2012_1
SRR2147728	Par_AnPN_2012_2
SRR2147739	Par_TM_2012_1
SRR2147819	Par_TM_2012_2
SRR2147825	Par_Nas_2013_1
SRR2147830	Par_Nas_2013_2
SRR2147835	Par_AnPN_2013_1
SRR2147840	Par_AnPN_2013_2
SRR2147845	Par_TM_2013_1
SRR2147853	Par_TM_2013_2
```

5. The .sra files will need to be downloaded via FTP from this link: ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/

```
```
####Converting a `.sra` file to `.fasta` file
```
```
####Generate a mapping `.txt` file
```
```
