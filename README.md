### STOPGAP Analysis Pipeline ###

Judong Shen & Kijoung Song

Feb. 6th, 2017



STOPGAP (Systematic Target OPportunity assessment by Genetic Association Predictions) is a catalog of genetic associations and the genes that influence human traits. STOPGAP is a complete rebuild of the previous version of the datasets described in the paper "Matthew R. Nelson et al., The support of human genetic evidence for approved drug indications, Nature Genetics, 2015, Aug;47(8):856-60". This version integrates more genetic association and functional genomics data to provide more comprehensive evaluation of the gene scores and variant-to-gene ranking hypotheses. The purpose of these data sets is primarily to support target validation, though there are a large variety of potential applications. STOPGAP uses various GWAS, LD data and functional annotation datasets, and some key algorithms for merging the GWAS datasets, generating LD r2 data, SNP locus clustering, variant-to-gene mapping, evaluating the gene scores and variant-to-gene ranking hypotheses to generate a set of STOPGAP datasets, which can be directly used for supporting target selection, validation, and repositioning decisions.

This STOPGAP analysis pipeline was implemented by R scripts, shell scripts, Python scripts and Perl scripts. The details of the scripts are as follows: 

 o STOPGAP2_functions.R: Most of the core functions are included in this file. These functions process and merge the multiple GWAS datasets, process various functional genomics datasets, identify high LD SNPs and calculate the LD r2 from 1000 Genomes datasets (through the ./STOPGAP2_LD/LDcalc.sh shell scripts), merge the high-LD SNPs with GWAS information and the functional genomics information, do the LD SNP locus clustering, map variants to genes, evaluate the gene scores and variant-to-gene ranking hypotheses and generate multiple levels of STOPGAP datasets. 
 o STOPGAP2_run.R: The pipeline scripts to run STOPGAP2_functions.R to generate STOPGAP datasets.  

 1. To run the scripts, please follow steps listed below.
	
	a) Download 'STOPGAP_source.tar.gz' first and extract it at your local directory. Please don't change directory or file names because the script (=STOPGAP2_functions.R) use the same names. 
	
	The details of the zipped folder are as follows:
  
	  a.1) Source/Data
	  
		  Genetic association data
		  GraspFullDataset2.zip: grasp data
		  gwas_catalog_v1.0-associations_e85_r2016-08-01.tsv: nhgri data
		  gwasdb_20150819_snp_trait.gz: gwasdb data
		  phewas-catalog.csv: phewas data
		  gencode_v19_clean.RData: Gencode data v19
		  MeSH_Aug2016.txt: the MeSH term data
		  omim.RData: OMIM&Orphanet dataset
		
        a.1.1) Source/Data/1KG_AF
		      gws.frq.RData: Allele frequencies calculated based on 1000G
 
	    a.1.2) Source/Data/genomic_resource
		    The functional genomics data
		    eQTL_single_44Tissues.RData and eQTL_multi_9Tissues.RData: eQTL data 
		    chic.RData: Promoter Capture Hi-C
		    CHIA.PET_5celltypes.RData: ENCODE ChIA-PET data
		    dhscor.RData: ENCODE DHS-promoter correlation data
		    FANTOM5.RData: FANTOM5 data
		    gwas.ld.deltaSVM.RData: deltaSVM data
		    gwas.ld.phylop.RData: phylop data
		    cato.RData: CATO score data
		    gwas.ld.vep.RData: VEP data
		
	  a.2) Source/STOPGAP2_LDResults
		  
		  LD results calculated based on the genetic association data
		  rsID_Coordinates.txt: Coordinate
		  chrN.labeled.ld, where N=1..22 and X: LD SNPs data

	b) Download STOPGAP2_functions.R and STOPGAP2_run.R at Source/
	
	c) Run STOPGAP2_run.R
		
		R --vanilla --slave < STOPGAP2_run.R

 o LDcalc.sh: shell scripts to run the LD calculation, which run vcftools to calculate LD and python scripts to label LD. 
 o Get_rsID_coord.py: python scripts to translate the rsids included in the GWAS association data to genomic coordinate. 
 o Label_LD_results.py: python scripts to label the LD results with alleles and IDs.		

2. LD calculation using the European sub-population of 1000 Genome panel
	
	a) Download their population allele frequencies based on the phase I 1000 Genomes data http://www.internationalgenome.org/data
	
	b) A typical run
		Coordinate lookup
	    python2.7 ./Get_rsID_coord.py -o output-directory -r rs-list
		LD Calculation 
      ./LDcalc.sh chromosome-number BED file-directory output-directory	
 
 ** Please note that this step will take a long time to finish so that we uploaded all LD results calculated based on the  genetic association data at Source/STOPGAP2_LDResults/chrN.labeled.ld, where N=1..22 and X. 


### STOPGAP data description ###


 o stopgap_chr1.RData, ..., stopgap_chrX.RData: The most comprehensive STOPGAP data (by chromosome) merged from GWAS data, SNP LD data, SNP cluster data and var2gene mapping data, including all possible ways to map LD variants to genes. 

 o stopgap.bestld.RData: This is grouped by publication-trait-GWAS and GWAS SNP-gene, reducing each reported genetic association to the best variant-to-gene hypothesis (highest gene score) for each possible gene mapping.

 o stopgap.gene.mesh.RData: This is grouped by gene-trait, selecting the best variant-to-gene mapping for all genetic associations that could connect a gene to a trait. Finally the data was merged with latest OMIM/Orphanet data.


 Alphabetical order for the definition of the columns in above STOPGAP datasets

 o af.1kg: the overall (across all the four populations) allele frequency of the snp.ld in the phase 1 1000 Genomes Project. 

 o asn.af.1kg: the allele frequency of the snp.ld in the Asian or East Asian population in the phase 1 1000 Genomes Project. 

 o amr.af.1kg: the allele frequency of the snp.ld in the Latin American population in the phase 1 1000 Genomes Project. 

 o afr.af.1kg: the allele frequency of the snp.ld in the African population in the phase 1 1000 Genomes Project. 

 o asso.count: the number of associations reported for this gene-MeSH combination in the bestld data (i.e. number of unique pubmedid's reporting this connection). 

 o cato.score: CATO score greater than and equal to 0.1 

 o cato.motifname: the position of the SNP relative to the transcription factor motif 

 o cato.cell: the cell types having a DNase hotspot over each SNP

 o chiapet.cell: the cell type of the CHIA-PET data for this snp.ld. 

 o chic.tissue: the tissue type of the Capture Hi-C data for this snp.ld

 o chr.ld: the chromosome of the snp.ld. 

 o cluster: the locus LD cluster formed by the correlated  SNPs, clustered by the SNP clustering algorithm.  

 o clust.init.gwassnp: the initial or seed snp.gwas which starts the clustering process within this cluster.  Refer to the SNP clustering algorithm for details. 

 o deltasvm.95p: the 95th percentile of deltaSVM scores across 230 cell types 

 o deltasvm.cell: top 5 cell types with daltaSVM scores

 o dhs.fpr: false positive rate based on the distance of the snp.ld falling within a correlated distal DHS site from the gene transcription start site, as well as the Pearson and Spearman correlations as described in FPR_Methods

 o dhs.type: the DHS type - whether the site is a distal (d) or promoter (p) region. 

 o disease: the name of the trait for the corresponding genetic association as provided by the source database. 

 o eqtl.tissue.score: the eQTL score p-value in the tissues or cell types 

 o evidence: the column collapses all of the sources of evidence connecting the variant to the gene. Here's the key for each position in the string, separated by ";".  
 
	1) VEP annotation: V = in gene, Vd = deleterious NS variant, Vn = neutral NS variant.
	2) eQTL: E = is an eQTL for the gene.
	3) CHi-C: CC = has a CHi-C link. The second digit indicates d = distal.
	4) ChIA-PET: CP = has a ChIA-PET link. The second digit indicates whether it is d = distal. 
	5) DHS correlation: D = has a DHS FPR < 0.85. The second digit indicates whether it is d = distal or p = promoter.
	6) FANTOM5: F = has a FANTOM5 FPR < 0.85. The second digit indicates d = distal.
	7) deltaSVM: DS = has a deltaSVM score >= 5.5.
	8) CATO: CT = has a CATO score >= 0.1.
	9) Phylop: p = has a phylop >= 1.5.

 o evidence.best: the evidence string of gene.best. 

 o eur.af.1kg: the allele frequency of the snp.ld in the European population in the phase 1 1000 Genomes Project. 

 o fantom5.fpr: false positive rate based on the distance of the snp.ld falling within a correlated distal FANTOM5 CAGE site from the gene transcription start siteas described FPR_Methods

 o fantom5.tissue: the cell tissue of the FANTOM5 data for this snp.ld.

 o gene: a gene that an association has been mapped to.

 o gene.best: the gene with the best variant-to-gene mapping evidence (gene.score) for a given snp.gwas.

 o gene.distance: the physical distance between snp.ld and gene. If snp.ld is within a gene, distance was assigned to 0.

 o gene.rank.max: the rank of the gene score for the given gene relative to the other possible gene mappings.  The "max" ranking gives scores with ties the largest rank, e.g. gene scores of 1, 1, 1, 2 would have a gene.rank.max of 3, 3, 3, 4.  

 o gene.end: gene end postion coordinated by GRCH37

 o gene.rank.min: similar to gene.rank.max, but using a "min" ranking algorithm, e.g. gene scores of 1, 1, 1, 2 would have a gene.rank.max of 1, 1, 1, 4. 

 o gene.score: calculated by multipling v2g.score and r2 (LD).

 o gene.start: a gene start postion coordinated by GRCH37.

 o init.samp: the initial sample and sample size for the GWAS. 

 o msh: Medical Subject Heading for disease, as provided by Mark Hurle. 

 o msh.cat: MeSH descriptors are organized in 16 categories: category A for anatomic terms, category B for organisms, C for diseases, D for drugs and chemicals, etc. Each category is further divided into subcategories. msh.tree: Within each subcategory, descriptors are arrayed hierarchically from most general to most specific in up to twelve hierarchical levels. 

 o num.cluster: the number of distinct clusters for this unique gene-MeSH combination in the bestld data, a reasonable proxy for the number of potential independent associated loci.

 o num.gene.cluster: the number of unique genes within top SNP cluster for this unique gene-MeSH combination in the bestld data.

 o num.gwas.snp: the number of unique GWAS SNPs for this unique gene-MeSH combination in the bestld data.

 o num.ld.snp: the number of unique LD SNPs for this unique gene-MeSH combination in the bestld data.

 o pics: an estimate of the probability that the given SNP is the causal variant given the observed pattern of association or LD at the locus.

 o pos.ld: the genome position of the snp.ld.  

 o pvalue: the p-value reported for the association of snp_id with disease.

 o pubmedid: the reference for the association: a PubMed ID for the published paper.

 o rep.samp: sample and sample size for subsequent replication(s). 

 o r2: LD between snp.gwas and snp.ld. The LD r2s are calculated from the phase 1 1000 Genomes Project genotype data by using a 500kb (either side) window size and a minimum r2 threshold 0.7. 

 o snp.gwas: the identifier (generally dbSNP141 RS ID) mapped from the original GWAS SNP id (snp.gwas.orig) of the SNPs reported to be associated with disease.

 o snp.gwas.orig:  the original GWAS SNP id of the SNPs reported to be associated with disease. 

 o snp.ld: a SNP in linkage disequilibrium (LD) with snp.gwas that provides a plausible connection to gene.  

 o source: the GWAS data source: NHGRI GWAS catalog, GRASP, GWASdb2, PheWAS and STABILITY/SOILD baseline association. Note the GWAS data are merged by the order of nhgri > grasp > gwasdb2 > PheWAS > STABILITY/SOLID. 

 o vep.phylop: 100-way vertebrate PhyloP score. 

 o vep.conseq: The consequence of snp.ld on the protein sequence from the Ensembl Variant Effect Prediction data. 

 o vep.aa: the amino acid change for snp.ld from the Ensembl Variant Effect Prediction (VEP) database. 

 o vep.condel.score: the functional prediction condel score of snp.ld from the Ensembl Variant Effect Prediction database.

 o vep.severity: severity scores defined according to gene consequence terms.

 o v2g.score: variant to gene causality mapping score. The contributions to v2g Score are summarized on a separate Gene Scoring Wiki page.

 
