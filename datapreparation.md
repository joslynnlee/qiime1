This tutorial was adapted from the [454 Overview Tutorial: de novo OTU picking and diversity analyses using 454 data](http://qiime.org/tutorials/tutorial.html)
Data for this tutorial is from the paper by *FAC Lopes et al*. [Microbial Community Profile and Water Quality in a Protected Area of the Caatinga Biome](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0148296)

###Learning Objectives
* Download raw data from NCBI's Sequence Read Archive (SRA)
* Understand file formats and conversion
* Generating input files for QIIME analysis

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

5. The .sra files will need to be downloaded via FTP from this link: [ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/](ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/)

####Converting a `.sra` file to `.fastq` file
Using the fast-dump program with the script converts the .sra to .fastq
```
$ ./fastq-dump --split-files SRR2146911.sra 
Read 22222 spots for SRR2146911.sra
Written 22222 spots for SRR2146911.sra
```
####Converting a `.fastq` file to `.fna` file using macqiime script
If you download the `.fastq` file, using `less` to view the file would yield:
```
$ less SRR2146911.fastq
@SRR2146911.1 1 length=78
ACGAGTGCGTTTAGATAACCTGGTAGCTAGCTCAGTACGAGACTGCCAAGGAAGTCGTAACAAGGTAACTAGCTCAGT
+SRR2146911.1 1 length=78
IIIIIIIHD666?IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIHHCCCCIIIIIIIIIIIIIIIIIIIIIIIIIII
@SRR2146911.2 2 length=66
ACGAGTGCGTATTAGATACCCGGTAGCTAGCTCAGTACTAAGTCGTAACAAGGTACCTAGCTCAGT
+SRR2146911.2 2 length=66
FFFHHHHHHIEBBBDE<5/////50/17<<<?AAAD??<99;<?;8445////76;<<:;;=GGGH
@SRR2146911.3 3 length=55
ACGAGTGCGTATTAGATACCCAGGTAGGAAGTCGTAACAAGGTACCTAGCTCAGT
+SRR2146911.3 3 length=55
IIIIIIIIIIFFDBGHD:///7449<////<CD?>>571133CFAAB>AAADFDG
@SRR2146911.4 4 length=60
ACGAGTGCGTATTAGATACCCTGGTAGCTCAGTAAGTCGTAACAAGGTACCTAGCTCAGT
+SRR2146911.4 4 length=60
HHHHHHHHHHHHHHHIG>666EEGHGGGIDEEEEEEEGHHHHHEEEEHHHGG@868@EE@
```
A `.fastq` format stores both a nucleotide sequence and its corresponding quality scores. A `.fastq` file normally uses four lines per sequence:

```
Line 1 begins with a '@' character and is followed by a sequence identifier and an optional description (like a FASTA title line).
Line 2 is the raw sequence letters.
Line 3 begins with a '+' character and is optionally followed by the same sequence identifier (and any description) again.
Line 4 encodes the quality values for the sequence in Line 2, and must contain the same number of symbols as letters in the sequence.
```
From the information about, how many sequences are in the `.fastq` file?

To exit out, use `q` to get out of the `less` mode.

When macqiime is intialized, the script `convert_fastaqual_fastq.py` can be used to convert `.fastq` to `.fna.`and `.qual`. It breaks down the `.fastq` file into the two separate files.
```
$ convert_fastaqual_fastq.py -c fastq_to_fastaqual -f SRR2146911.fastq -o SRR2146911
```
The output file `SRR2146911` will contain the two files `.fna` and `.qual`.

###Formatting files for QIIME
File formats are important for QIIME analysis. The three types of files needed are:
* mapping file
* fasta files `.fasta` or `.fna` format
* quality score files `.qual`

####Generate a mapping `.txt` file
For the full details of the mapping file, see the [Mapping File Overview](http://qiime.org/documentation/file_formats.html#mapping-file-overview).The mapping file is generated by the user. This file contains all of the information about the samples necessary to perform the data analysis. The mapping file is generated by the user. This file contains all of the information about the samples necessary to perform the data analysis. In general, the mapping file should contain the name of each sample, the barcode sequence used for each sample, the linker/primer sequence used to amplify the sample, and a description column. Here is an example:
```
#SampleID	  BarcodeSequence	LinkerPrimerSequence	Treatment	DOB	      Description
#Example mapping file for the QIIME analysis Paraguacu River samples					
P1W.1	      ACGAGTGCGT	    ATTAGATACCCNGGTAG	    Wet	      20121128	P1_wet_season_replicate_1
P1W.2	      ACGCTCGACA	    ATTAGATACCCNGGTAG    	Wet     	20121128	P1_wet_season_replicate_2
P2W.1	      AGACGCACTC	    ATTAGATACCCNGGTAG   	Wet     	20121126	P2_wet_season_replicate_1
P2W.2	      AGCACTGTAG	    ATTAGATACCCNGGTAG   	Wet     	20121126	paraguacu_P2_Wet_2
P3W.1       ATCAGACACG      ATTAGATACCCNGGTAG     Wet       20121126	paraguacu_P3_Wet_1
P3W.2	      ATATCGCGAG	    ATTAGATACCCNGGTAG	    Wet	      20121126	paraguacu_P3_Wet_2
P1D.1	      CGTGTCTCTA	    ATTAGATACCCNGGTAG	    Dry	      20130227	paraguacu_P1_Dry_1
P1D.2	      CTCGCGTGTC	    ATTAGATACCCNGGTAG	    Dry	      20130227	paraguacu_P1_Dry_2
P2D.1	      TAGTATCAGC	    ATTAGATACCCNGGTAG	    Dry	      20130226	paraguacu_P2_Dry_1
P2D.2	      TCTCTATGCG	    ATTAGATACCCNGGTAG	    Dry	      20130226	paraguacu_P2_Dry_2
P3D.1	      TGATACGTCT	    ATTAGATACCCNGGTAG	    Dry	      20130226	paraguacu_P3_Dry_1
P3D.2	      CATAGTAGTG	    ATTAGATACCCNGGTAG	    Dry	      20130226	paraguacu_P3_Dry_2
```
Column 1: name of each sample
Column 2: the barcode sequence used for each sample
Column 3: the linker/primer sequence used to amplify the sample
Column 4-6: description columns

In the FAS Lopes paper, the forward primer was listed in the methods. The barcodes were taken from the .fastq files. The sample names were given based on the treatment group and replicate name. The description columns were listed based on information from the metadata associated from the SRA information.

####Look at the `.fna` and `.qual` formats
Let's look at the `.fna` file by using `less` that we learned earlier.
```
$ less P1P2P3_wetdry.fna
>SRR2146911.1
ACGAGTGCGTTTAGATAACCTGGTAGCTAGCTCAGTACGAGACTGCCAAGGAAGTCGTAACAAGGTAACTAGCTCAGT
>SRR2146911.2
ACGAGTGCGTATTAGATACCCGGTAGCTAGCTCAGTACTAAGTCGTAACAAGGTACCTAGCTCAGT
>SRR2146911.3
ACGAGTGCGTATTAGATACCCAGGTAGGAAGTCGTAACAAGGTACCTAGCTCAGT
>SRR2146911.4
ACGAGTGCGTATTAGATACCCTGGTAGCTCAGTAAGTCGTAACAAGGTACCTAGCTCAGT
>SRR2146911.5
ACGAGTGCGTATTAGATACCCTGTAGCTCAGTAAGTCGTAACAAGGTAGCTAGCTCAGT
```
To exit out, use `q` to get out of the `less` mode.

Next we will check the format of the `.qual` file. Use the same exit, strategy.
```
$ less P1P2P3_rep1_wetdry.qual
>SRR2146911.1
40 40 40 40 40 40 40 39 35 21 21 21 30 40 40 40 40 40 40 40 40 40 40 40 40 40 4$
40 40 40 40 40 40 40 40 40 40 40 40 40 40 40 40 40 40
>SRR2146911.2
37 37 37 39 39 39 39 39 39 40 36 33 33 33 35 36 27 20 14 14 14 14 14 20 15 14 1$
26 28 38 38 38 39
>SRR2146911.3
40 40 40 40 40 40 40 40 40 40 37 37 35 33 38 39 35 25 14 14 14 22 19 19 24 27 1$
>SRR2146911.4
39 39 39 39 39 39 39 39 39 39 39 39 39 39 39 40 38 29 21 21 21 36 36 38 39 38 3$
>SRR2146911.5
40 40 40 40 40 40 40 40 40 40 40 40 38 38 36 36 30 25 16 16 16 22 22 22 22 25 2$
```
Let's see if the  number of lines are equal for the `.fna` and `.qual` files using `grep`:
```
$ grep -c ">" P1P2P3_rep1_wetdry.fna 
93868
$ grep -c ">" P1P2P3_rep1_wetdry.qual 
93868
```
Great job! Now that we know the three input files, we can begin the QIIME workflow!
