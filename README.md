DACRE-scan

Author: Fang Wang
Email: fwang9@mdanderson.org or kchen3@mdanderson.org
Draft date: Feb. 12, 2018

Description
===========
DACRE-scan is a tool used in cancer genomic studies to discover single nucleotide variants (both somatic and germline) 
with differential allelic cis-regulatory effects (DACRE) through an integration of whole- exome sequencing (WES) data and whole transcriptome sequencing (WTS) data.

It reports estimations of tumor purity at both the DNA and the RNA levels, intratumor heterogeneity at the DNA level, allelic specific copy numbers and expression levels and  
DACRE (functional) score of each variant allele. If the input only includes information at the DNA level, it only reports the estimates at the DNA level and 
focuses on somatic mutations. 

System requirements and dependency
==================================
DACRE-scan runs on a x86_64 Linux system. It depends on samtools and bedtools to extract reads covering variant sites from WTS data. 
It requires R(version >= 3.2)
to run and has dependency on the R packages: bbmle, emdbook, copynumber,TitanCNA, facets, mixtools, ASCAT and Sequenza. 
These R packeages are already included in this release.

Installation
============
Please download and copy the distribution to your specific location. 

For example, if the downloaded distribuition is DACRE_scan.tar.gz.
	Type 'tar zxvf DACRE_scan.tar.gz'

Then, run DACRE-scan.py in the resulting folder.

Usage
=====
Input files: 

DNA input files: 

	Two kinds of input files are allowed in DACRE-scan:

	(1) the output generated by Varscan, which includes both germline and somatic variants through -v;

	(2) two seperate files: one contains germline variants through -g and the other contains somatic mutations through -s. 

	The format of these two files are the same. It should include 8 tab-delimited columns: 
		chromosome (start from "chr"), 
		position, 
		reference allele, 
		alternative allele, 
		ref allelic read counts in the normal sample, 
		alt allelic read counts in the normal sample, 
		ref allelic read counts in the tumor sample,
		alt allelic read counts in the tumor sample.

RNA input files:

	Two kinds of input files are allowed in DACRE-scan:

	(1) a RNA-seq bam file. Load samtools and bedtools first if you input bam file.

	(2) allelic read counts of mutation from the RNA-seq bam, which includes 7 tab-delimited cloumns: 
		chromosome (start from "chr"),
		position,
		reference allele, 
		alternative allele, 
		reference allelic read count from the RNA-seq bam,
		alternative allelic read count from the RNA-seq bam,
		type of mutation (germline or somatic).    

Output files: 

	There are Multiple files: 

	If the input includes a RNA file, DACRE-scan would generate output at both the DNA and the RNA levels. 
	Otherwise, it will output only estimation at the DNA level.

	(1) .segment file: 
		position of copy nuymber segments; 
		allele-specific copy number (Dmajor and Dminor), 
		allele-specific expression levels(Rmajor and Rminor), and 
		posterior probability of discordant expression corresponding to each segment.

	(2) .mutation file: 
		position of mutations, 
		reference(ref) and alternative(alt) allele, 
		allelic read count from RNA-seq(refNum and altNum),
		mutation type(germline or somatic), 
		allele specific copy number (altD corresponding to alternative allele and wildD corresponding to reference allele),
		allele specific expression level (altR corresponding to alternative allele and wildR corresponding to reference allele),
		posterior probability of discordant expression and DACRE score for each mutation.

	(3) .summary file: 
		tumor purity at the DNA and/or the RNA level; 
		ploidy and intra-tumor heterogeneity at the DNA level

Run DACRE-scan:
***The python script for easy run of DACRE-scan is in the release directory. You can tune the
parameters as you wish.***

Python DACRE-scan.py –p Rscript/path –g germline.input –s somatic.input –r RNA.bam <-t iter.optimal.index> <-e mutation.expression> <-o output.path>

About the default parameters
========================
DACRE-scan optimizes estimation of tumor purity and allele specific copy numbers by combining both germline SNPs and somatic SNVs. 
By default, -t is 1, which turns on iterative optimization.

A user can set -t 0 to turn off the iterative optimization.


Example
=====
python DACRE-scan.py -g TCGA-3C-AAAU-01A-11D-A41F-09.snp.germline.input -s TCGA-3C-AAAU-10A-01D-A41F-09.mutect.vcf -e TCGA-3C-AAAU.RNA.SNV -p segACN
