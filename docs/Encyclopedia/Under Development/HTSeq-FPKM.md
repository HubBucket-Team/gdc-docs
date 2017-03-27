# HTSeq-FPKM #
## Description ##

Fragments per Kilobase of transcript per Million mapped reads (FPKM) is a simple expression level normalization method. The FPKM normalizes read count based on gene length and the total number of mapped reads.  

## Overview ##

FPKM is implemented at the GDC on gene-level read counts that are produced by HTSeq and generated using custom scripts. The formula used to generate FPKM values is as follows:

FPKM = [RM<sub>g</sub> * 10<sup>9</sup> ] / [RM<sub>t</sub> * L]

* RM<sub>g</sub>: The number of reads mapped to the gene
* RM<sub>t</sub>: The total number of read mapped to protein-coding sequences in the alignment
* L: The length of the gene in base pairs

The scalar (10<sup>9</sup>) is added to normalize the data to "__kilo__ base" and "__million__ mapped reads."

See [HTSeq-FPKM-UQ](LINK) for an alternative method of gene expression level normalization.

### Tools ###
## References ##
1. [GDC mRNA-Seq Documentation](https://docs.gdc.cancer.gov/Data/Bioinformatics_Pipelines/Expression_mRNA_Pipeline/)
2. [HTSeq Website](http://www-huber.embl.de/users/anders/HTSeq/doc/overview.html)
3. Anders, S., Pyl, P.T. and Huber, W., 2014. HTSeq–a Python framework to work with high-throughput sequencing data. Bioinformatics, p.btu638.


## External Links ##

Categories: Workflow Type
