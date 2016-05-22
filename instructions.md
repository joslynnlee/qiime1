This tutorial was adapted from the [454 Overview Tutorial: de novo OTU picking and diversity analyses using 454 data](http://qiime.org/tutorials/tutorial.html)

###Learning objectives
* Introduce the workflow of QIIME
* 

Data for this tutorial is from the paper by FAC Lopes *et al*.
[Microbial Community Profile and Water Quality in a Protected Area of the Caatinga Biome](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0148296)
Go here for [data preparation](datapreparation.md) of the NCBI SRA samples.

This QIIME analysis explains how to apply de novo OTU picking and diversity analysis to 16S amplicon data. In 1985, the National Park of Chapada Diamantina (PNCD) was created to prevent environmental degradation. The presence of bacteria capable of pesticide degradation and assimilation, evidencing possible anthropogenic impacts on the Caatinga. The data was collected to evaluate the effect of PNCD protection on the water quality and microbial community diversity of this river by analyzing water samples obtained from points located inside and outside the PNCD in both wet and dry seasons. We will be analyzing the data with a modified workflow for the workshop.

Here is a workflow to follow for the analysis we are going to perform:
(insert image)

#### Getting into your directory
Here we will use commands that we learned in the earlier session to get into the directory (file) that holds our `.fasta`, `.fna` and `mapping.txt` files. This directory will hold all files we will be using throughout the analysis. 

#### Step 1. Identifying the version of macqiime
In the paper under Materials and Methods 'Polymerase chain reaction, 16S rRNA gene amplicon sequencing, and sequence analysis' section, the authors analyzed the sequences using QIIME 1.7.0 software. On our computers we will be checking the version of our QIIME. This will impact the availability of certain scripts and parameters. 

To initialize macqiime, type the command:
```
$ macqiime
```
In this we can begin to execute a script `.py` to check the version. QIIME has its own script to check this. We are going to see the options that come with this script. Remember `-h` was used to check the parameters/arguments necessary.
```
$ print_qiime_config.py –h
Usage: print_qiime_config.py [options] {}

[] indicates optional input (order unimportant)
{} indicates required input (order unimportant)

Print QIIME configuration details and optionally perform tests of the QIIME base or full install.

Example usage: 
Print help message and exit
 print_qiime_config.py -h

Example 1: Print basic QIIME configuration details
 print_qiime_config.py

Example 2: Print basic QIIME configuration details and test the base QIIME installation
 print_qiime_config.py -t

Example 3: Print basic QIIME configuration details and test the full QIIME installation
 print_qiime_config.py -tf

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -v, --verbose         Print information during execution -- useful for
                        debugging [default: False]
  -t, --test            Test the QIIME install and configuration [default:
                        False]
  -f, --qiime_full_install
                        If passed, report on dependencies required for the
                        QIIME full install. To perform tests of the QIIME full
                        install, you must also pass -t. [default: False]
```
We will use Example 2 to check the version and run a test on the installation. Type:
```
$ print_qiime_config.py –t
```
Read the output and write down the version. How many tests were performed?

#### Step 2. Validate the format of the mapping file
The important file for this analysis is the mapping file. To ensure it is correctly formatted the script `validate_mapping_file.py` is used. In the script below we have two options:
`-m` and `-o`
The mapping file `watertest_Map.txt` can be found in the folder `//`.
```
$ validate_mapping_file.py -m watertest_Map.txt -o mapping_output
No errors or warnings were found in mapping file.
```
This script will print a message indicating whether or not problems were found in the mapping file. Here there isn't a problem.

There are four output files. If there are errors, the HTML file shows the location of errors and warnings and a plain text log file will also be created. Errors will cause fatal problems with subsequent scripts and must be corrected before moving forward. Warnings will not cause fatal problems, but it is encouraged that you fix these problems as they are often indicative of typos in your mapping file. A file ending with _corrected.txt will have a copy of the mapping file with invalid characters replaced by underscores.

#### Step 3. Quality of the reads 
The next step is to assess the quality of the reads coming off the sequencer. The script to use here is `quality_scores_plot.py` which requires the `.qual` file. Here the option/parameter `'q` calls the `.qual` file and `o` generates the output file `quality_histogram.`
```
$ quality_scores_plot.py -q P1P2P3_rep1_wetdry.qual -o quality_histogram
quality_histogram
Suggested nucleotide truncation position (None if quality score average did not fall below the minimum score parameter): 439
```
Generate two histograms of sequence quality scores. High-quality read length and abundance are the primary factors differentiating correct from erroneous reads produced by sequencing instruments.

#### Step 4. Preprocess the sequence reads 
Assign the reads to samples based on their nucleotide barcode. Perform quality filtering, removing any low quality or ambiguous reads.
```
$ split_libraries.py -b 10 -m watertest_Map.txt -f P1P2P3_rep1_wetdry.fna -q P1P2P3_rep1_wetdry.qual -o split_library_output
```
This will create three files in the new directory `split_library_output/`. To view them let's look into the output file:
```
$ cd split_library_output/
```
Now we need to list the files. Type in:
```
$ ls
```
We should see three files.

`split_library_log.txt `: This file contains the summary of demultiplexing and quality filtering, including the number of reads detected for each sample and a brief summary of any reads that were removed due to quality considerations.
`histograms.txt`: This tab-delimited file shows the number of reads at regular size intervals before and after splitting the library.
`seqs.fna`: This is a fasta formatted file where each sequence is renamed according to the sample it came from. The header line also contains the name of the read in the input fasta file and information on any barcode errors that were corrected.

Let's look at the `split_library_log.txt` file to find out more information about. Type in `nano`:
```
$ nano split_library_log.txt
```
Write down the number of sequences before and after. 

To exit out of nano, CRTL-X will get you out of nano view. 

#### Step 5. Build an operational taxonomic unit (OTU) table 
```
$ pick_de_novo_otus.py -i split_library_output/seqs.fna -o otus
```
#### Step 6. Summarize sample OTU table/counts 
```
$ biom summarize-table -i otus/otu_table.biom –o otus/stats_OTU_table.txt
```
#### Step 7. Summarize communities by taxonomic composition 
```
$ summarize_taxa_through_plots.py -i otus/otu_table.biom -m watertest_Map.txt -o taxa_summary
```
#### Step 8. Make a taxonomy heatmap 
```
$ make_otu_heatmap.py -i taxa_summary/otu_table_L5.biom -c Treatment -m watertest_Map.txt -o taxa_summary/otu_table_L5_heatmap.pdf
```
#### Step 9. Analyzing the OTU table: alpha diversity 
```
$ alpha_rarefaction.py -i otus/otu_table.biom -m watertest_Map.txt -t otus/rep_set.tre --retain_intermediate_files -e #### -o arare_intermediate_####
```
#### Step 10. Analyzing the OTU table: beta diversity 
```
$ beta_diversity_through_plots.py -i otus/otu_table.biom -m watertest_Map.txt -t otus/rep_set.tre -e 100 -o beta_div100
```
#### Step 11. Generate 3-D biplots for beta diversity
```
$ make_emperor.py -i beta_div100/unweighted_unifrac_pc.txt -m watertest_Map.txt -t taxa_summary/otu_table_L5.txt --n_taxa_to_keep 10 -o biplots_betadiv_taxa10
```

