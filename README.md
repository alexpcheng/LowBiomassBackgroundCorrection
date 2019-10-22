###Low Biomass Background Correction

Author: Philip Burnham

Link to preprint: https://www.biorxiv.org/content/10.1101/734756v1


Purpose: The following is a guide to reduce the background contamination of microbial reads in a cell-free DNA sequencing set. We will show how to use the pipeline and its functions to denoise a dataset from urine samples taken from patients who had received kidney transplants at the New York Presbyterian Hospital in NYC, USA. The data includes samples with a matched same day diagnosis of either E. coli or Enterococcus urinary tract infection. The dataset also includes patients who had no observable bacteria in the urinary tract, and who had no UTI during the time they were monitored (this is our control group).


Outline: The pipeline is implemented in R, and takes advantage of two observations: 1) the variation in coverage across microbial genomes, 2) how the abundance of bacteria changes with the total mass of DNA added to library preparation. In (1), microbial genomes with high coverage variability (measured by the coefficient of variation in coverage) are interpreted as digital noise. In (2), if the representation of a microbe in a batch decreases with increasing amounts of input biomass, it is likely a contaminant, presenting itself at low abundance.


Step 0: Collecting the needed files

Prior to implementing this algorithm, we have implemented a bioinformatics pipeline that: 1) removes low quality DNA reads, 2) aligns reads to the human reference genome (UCSC hg19 build), 3) aligns human-unmapped reads to a large microbial genome reference database (NCBI blast), and (4) estimates the abundance of bacteria based on alignment statistics. This pipeline has been used extensively by our lab in a variety of contexts including plasma, urine, amniotic fluid, and peritoneal dialysis effluent (paper coming).

From this pipeline we collect two important files:

The alignment statistics of each nonhuman read to a microbe (\*.tblat.1)

The phylogeny and abundance table that shows the microbiome estimated from all nonhuman reads (\*.grammy.tab).


For the purposes of this tutorial, we avoid providing raw FASTQ files for privacy and storage concerns. In practice, the LBBC algorithm will calculate the number of sequencing reads to look at batch covariation. This can be bypassed by directly providing a \*.tab file with the number of sequencing reads.

We also take note of the batches that our samples are in, and the estimated biomass (in ng) of the cell-free DNA we are using in our library preparation. This is our metadata file.

*If you are missing the \*.tblat.1 file, don’t worry! If you didn’t measure your DNA before the library preparation, don’t worry! The pipeline can be altered to ignore these steps (though the corresponding step in filtering will be ignored).*
