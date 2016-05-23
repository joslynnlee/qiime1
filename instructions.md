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
The mapping file `watertest_Map.txt` can be found in the folder `/insertdirectorypathname/`.
```
$ validate_mapping_file.py -m watertest_Map.txt -o mapping_output
No errors or warnings were found in mapping file.
```
This script will print a message indicating whether or not problems were found in the mapping file. Here there isn't a problem.

There are four output files. If there are errors, the HTML file shows the location of errors and warnings and a plain text log file will also be created. Errors will cause fatal problems with subsequent scripts and must be corrected before moving forward. Warnings will not cause fatal problems, but it is encouraged that you fix these problems as they are often indicative of typos in your mapping file. A file ending with _corrected.txt will have a copy of the mapping file with invalid characters replaced by underscores.

#### Step 3. Quality of the reads 
The next step is to assess the quality of the reads coming off the sequencer. The script to use here is `quality_scores_plot.py` which requires the `.qual` file. Here the option/parameter `-q` calls the `.qual`quality scores file and `o` generates the output file `quality_histogram.`
```
$ quality_scores_plot.py -q P1P2P3_rep1_wetdry.qual -o quality_histogram
quality_histogram
Suggested nucleotide truncation position (None if quality score average did not fall below the minimum score parameter): 439
```
Generate two histograms of sequence quality scores. High-quality read length and abundance are the primary factors differentiating correct from erroneous reads produced by sequencing instruments.

#### Step 4. Preprocess the sequence reads 
(The next task is to assign the multiplexed reads to samples based on their nucleotide barcode (this is known as demultiplexing). This step also performs quality filtering based on the characteristics of each sequence, removing any low quality or ambiguous reads.)
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
We should see three files:
* `split_library_log.txt `: This file contains the summary of demultiplexing and quality filtering, including the number of reads detected for each sample and a brief summary of any reads that were removed due to quality considerations.
* `histograms.txt`: This tab-delimited file shows the number of reads at regular size intervals before and after splitting the library.
* `seqs.fna`: This is a fasta formatted file where each sequence is renamed according to the sample it came from. The header line also contains the name of the read in the input fasta file and information on any barcode errors that were corrected.

Let's look at the `split_library_log.txt` file to find out more information about. Type in `nano`:
```
$ nano split_library_log.txt
```
The first several lines of the `split_library_log.txt` have the number of raw input sequences and the total number seqs written. Write down the number of sequences before and after.

Another line to pay attention to is the the line:
````
Sample ct min/max/mean: 3369 / 51312 / 13153.83
```
The min will be important for Step 9. To exit out of nano, CRTL-X will get you out of nano view. 

#### Step 5. Build an operational taxonomic unit (OTU) table 
In a de novo OTU picking process, reads are clustered against one another without any external reference sequence collection. `de novo OTU picking.py` is the primary interface for de novo OTU picking in QIIME. 
```
$ pick_de_novo_otus.py -i split_library_output/seqs.fna -o otus
```
(all of the sequences from all of the samples will be clustered into Operational Taxonomic Units (OTUs) based on their sequence similarity. OTUs in QIIME are clusters of sequences, frequently intended to represent some degree of taxonomic relatedness.Since each OTU may be made up of many related sequences, we will pick a representative sequence from each OTU for downstream analysis. This representative sequence will be used for taxonomic identification of the OTU and phylogenetic alignment. QIIME uses the OTU file created above and extracts a representative sequence from the fasta file by one of several methods.)

#### Step 6. Summarize sample OTU table/counts 
To view summary statistics of the OTU table, run:
the sequences present are fairly evenly distributed among the 9 microbial communities.
```
$ biom summarize-table -i otus/otu_table.biom –o otus/stats_OTU_table.txt
```
#### Step 7. Summarize communities by taxonomic composition 
You can group OTUs by different taxonomic levels (phylum, class, family, etc.) with the workflow script summarize_taxa_through_plots.py. Note that this process depends directly on the method used to assign taxonomic information to OTUS (see Assigning Taxonomy above):
```
$ summarize_taxa_through_plots.py -i otus/otu_table.biom -m watertest_Map.txt -o taxa_summary
```
The script will generate new tables at various taxonomic levels (we’ll refer to these as taxonomy tables, which are different than OTU tables). For example, the class-level table is located at taxa_summary/otu_table_L3.txt. Each taxonomy table contains the relative abundances of taxa within each sample:
```

```
To view the resulting charts, open the area or bar chart html file located in the taxa_summary/taxa_summary_plots folder. The following chart shows the taxonomic assignments for each sample as a bar chart. You can mouse-over the plot to see which taxa are contributing to the percentage shown:
#### Step 8. Make a taxonomy heatmap 

FIXED:
QIIME supports generating heatmap images of BIOM tables (e.g., OTU tables or the taxonomy tables generated in the previous step) with make_otu_heatmap.py. Let’s create a heatmap illustrating class-level abundances on a per-sample basis, where samples are sorted by whether they are from control or fasted mice:

```
$ make_otu_heatmap.py -i taxa_summary/otu_table_L5.biom -c Treatment -m watertest_Map.txt -o taxa_summary/otu_table_L5_heatmap.pdf
```
A PDF file is created as taxa_summary/otu_table_L3_heatmap.pdf. The first four samples are from fasted mice and the last five are from controls. This clearly illustrates class-level differences in the taxonomic composition of the samples:

#### Step 9. Analyzing the OTU table: alpha diversity 
Community ecologists are often interested in computing alpha (or the within-sample) diversity for samples or groups of samples in their study. Here, we will determine the level of alpha diversity in our samples using QIIME’s alpha_rarefaction.py workflow, which performs the following steps:

* Generate rarefied OTU tables (multiple_rarefactions.py)
* Compute measures of alpha diversity for each rarefied OTU table (alpha_diversity.py)
* Collate alpha diversity results (collate_alpha.py)
* Generate alpha rarefaction plots (make_rarefaction_plots.py)

Although we could run this workflow with the (sensible) default parameters, this provides an opportunity to illustrate the use of custom parameters in a QIIME workflow. To see what measures of alpha diversity will be computed by default, run:
```
$ alpha_rarefaction.py -i otus/otu_table.biom -m watertest_Map.txt -t otus/rep_set.tre --retain_intermediate_files -e #### -o arare_intermediate_####
```
To view the alpha rarefaction plots, open the file arare/alpha_rarefaction_plots/rarefaction_plots.html. Once the browser window is open, select the metric PD_whole_tree and the category Treatment, to reveal a plot like the figure below. You can click on the triangle next to each label in the legend to see all the samples that contribute to that category. Below each plot is a table displaying average values for each measure of alpha diversity for each group of samples in the specified category.

Select a metric: PD_whole_tree: `Treatment`
Show Categories: `INSERT`

#### Step 10. Analyzing the OTU table: beta diversity 
In addition to alpha (or within-sample) diversity, community ecologists are often interested in computing beta (or the between-sample) diversity between all pairs of samples in their study.

Beta diversity represents the explicit comparison of microbial (or other) communities based on their composition. Beta diversity metrics thus assess the differences between microbial communities. The fundamental output of these comparisons is a square, hollow matrix where a “distance” or dissimilarity is calculated between every pair of community samples, reflecting the dissimilarity between those samples. The data in this distance matrix can be visualized with analyses such as Principal Coordinates Analysis (PCoA) and hierarchical clustering.

Here, we will calculate beta diversity between our 9 microbial communities using the default beta diversity metrics of weighted and unweighted UniFrac, which are phylogenetic measures used extensively in recent microbial community sequencing projects. To perform this analysis, we will use the beta_diversity_through_plots.py workflow, which performs the following steps:

* Rarefy OTU table to remove sampling depth heterogeneity (single_rarefaction.py)
* Compute beta diversity (beta_diversity.py)
* Run Principal Coordinates Analysis (principal_coordinates.py)
* Generate Emperor PCoA plots (make_emperor.py)

We can run the beta_diversity_through_plots.py workflow with the following command, which requires the OTU table (-i) and tree file (-t) from above, the metadata mapping file (-m), and the number of sequences per sample (-e, even sampling depth):

```
$ beta_diversity_through_plots.py -i otus/otu_table.biom -m watertest_Map.txt -t otus/rep_set.tre -e 100 -o beta_div100
```
Beta diversity metrics assess the differences between microbial communities. By default, QIIME calculates both weighted and unweighted UniFrac, which are phylogenetically-aware measures of beta diversity.

The resulting distance matrices (bdiv_even146/unweighted_unifrac_dm.txt and bdiv_even146/weighted_unifrac_dm.txt) are the basis for further analyses and visualizations (e.g., Principal Coordinates Analysis and hierarchical clustering).

#### Step 11. Generate 3-D biplots for beta diversity
We can add taxa from the taxonomy tables in the taxa_summary/ directory to a 3-D PCoA plot using Emperor’s make_emperor.py. The coordinates of a given taxon are plotted as a weighted average of the coordinates of all samples, where the weights are the relative abundances of the taxon in the samples. The size of the sphere representing a taxon is proportional to the mean relative abundance of the taxon across all samples. The following command creates a biplot displaying the 5 most abundant class-level taxa:

```
$ make_emperor.py -i beta_div100/unweighted_unifrac_pc.txt -m watertest_Map.txt -t taxa_summary/otu_table_L5.txt --n_taxa_to_keep 10 -o biplots_betadiv_taxa10
```
The resulting html file biplots/index.html shows a biplot similar to this:
(INSERT) PCoA plot

