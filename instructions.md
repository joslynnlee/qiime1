This tutorial was adapted from the [454 Overview Tutorial: de novo OTU picking and diversity analyses using 454 data](http://qiime.org/tutorials/tutorial.html)
###Learning objectives

Data for this tutorial is from the paper by FAC Lopes *et al*.
[Microbial Community Profile and Water Quality in a Protected Area of the Caatinga Biome](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0148296)
Go [here for data preparation] of the NCBI SRA samples.

#### Step 1. Identifying the version of macqiime

```
print_qiime_config.py –t
```
#### Step 2. Validate the format of the mapping file
```
validate_mapping_file.py -m watertest_Map.txt -o mapping_output
```
#### Step 3. Quality of the reads 
```
quality_scores_plot.py -q P1P2P3_rep1_wetdry.qual -o quality_histogram
```
#### Step 4. Preprocess the sequence reads 
```
split_libraries.py -b 10 -m watertest_Map.txt -f P1P2P3_rep1_wetdry.fna -q P1P2P3_rep1_wetdry.qual -o split_library_output
```
#### Step 5. Build an operational taxonomic unit (OTU) table 
```
pick_de_novo_otus.py -i split_library_output/seqs.fna -o otus
```
#### Step 6. Summarize sample OTU table/counts 
```
biom summarize-table -i otus/otu_table.biom –o otus/stats_OTU_table.txt
```
#### Step 7. Summarize communities by taxonomic composition 
```
summarize_taxa_through_plots.py -i otus/otu_table.biom -m watertest_Map.txt -o taxa_summary
```
#### Step 8. Make a taxonomy heatmap 
```
make_otu_heatmap.py -i taxa_summary/otu_table_L5.biom -c Treatment -m watertest_Map.txt -o taxa_summary/otu_table_L5_heatmap.pdf
```
#### Step 9. Analyzing the OTU table: alpha diversity 
```
alpha_rarefaction.py -i otus/otu_table.biom -m watertest_Map.txt -t otus/rep_set.tre --retain_intermediate_files -e #### -o arare_intermediate_####
```
#### Step 10. Analyzing the OTU table: beta diversity 
```
beta_diversity_through_plots.py -i otus/otu_table.biom -m watertest_Map.txt -t otus/rep_set.tre -e 100 -o beta_div100
```
#### Step 11. Generate 3-D biplots for beta diversity
```
make_emperor.py -i beta_div100/unweighted_unifrac_pc.txt -m watertest_Map.txt -t taxa_summary/otu_table_L5.txt --n_taxa_to_keep 10 -o biplots_betadiv_taxa10
```

