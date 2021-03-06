# LDJump
**LDJump** is an R package to estimate variable recombination rates from population genetic data. 
It is a unix based program (with a necessary installation of LDhat (see <https://github.com/auton1/LDhat>)), able to estimate the recombination map of sequences in fasta and vcf format. 
First, the sequences are divided in short segments of user defined length. The constant recombination rate is estimated for every segment with a regression model. 
This set of estimates is fed in a segmentation algorithm (SMUCE) to estimate the breakpoints of the recombination landscape. A [PDF Manual](./Sources/LDJump.pdf) with complete documentation of each function is also available. The preprint of the current working paper can be found [here](<https://doi.org/10.1101/190876>).

### Author (Requests)
Please contact me in case of questions, comments, bug reports, etc...

    Author: Philipp Hermann
    E-Mail: philipp.hermann@jku.at

## Dependencies & System Requirements
This package makes use of several functions of other R-packages, as well as of [LDhat](<https://github.com/auton1/LDhat>) listed as follows: 

* Unix Operating System
* R (>= 2.10)
* adegenet (>= 2.0.1)
* ape
* genetics (>= 1.3.8.1)
* Biostrings (>= 2.38.4)
* stepR
* seqinr (>= 3.1-3)
* quantreg
* vcfR (>= 1.5.0)
* LDhat (2.2)
* snow

The uploaded version in *[Sources](./Sources)* and the listed packages need to be installed manually. Notice that **Biostrings** needs to be installed via [Bioconductor](<http://bioconductor.org/packages/release/bioc/html/Biostrings.html>).  [LDhat](<https://github.com/auton1/LDhat>) as well as the functions *dos2unix* and *awk* neeed to be installed too. 


## Installation
The ZIP-File of the package can be downloaded via "Clone or download" as well as the command line: 

```markdown
git clone https://github.com/PhHermann/LDJump.git
R CMD INSTALL LDJump
``` 

Using *R* one can also install the *.tar.gz* file with the following command: 
```R
install.packages("/PathToLDJump/LDJump_<version>.tar.gz", repos=NULL, type="source")
```

## Usage

After loading the package in the workspace one can use the main function *LDJump* in order to estimate the variable recombination rate for the population of interest. We recommend to use **LDJump** with the population in *fasta*-Format. Alternatively, restructuring of *vcf*-Files is also possible with a implemented function *vcfR_to_fasta*. Therefore, we used the reference sequence from <http://phase3browser.1000genomes.org/Homo_sapiens> for our example: 

```R
require(LDJump)
LDJump(seqName, alpha = 0.05, segLength = 1000, pathLDhat = "", format = "fasta", refName = NULL, start = NULL, 
       thth = 0.01, constant = F, status = T, cores = 1, accept = F, demography = F)
```

Detailed descriptions of the main functions and all adjacent functions computing the recombination map can be found via e.g.

```R
?LDJump
```

We provide examples with files in *[Example](./Example)* in addition to a set of Lookup-tables of LDhat in *[Lookup Tables](./Lookups)*. 

**LDJump** can also be used under a set of type I error probabilities alpha. Therefore, the parameter *alpha* needs to be fed with a vector of values such as:

```R
require(LDJump)
LDJump(seqName, alpha = c(0.1, 0.05, 0.01), segLength = 1000, pathLDhat = "", format = "fasta", refName = NULL, 
       start = NULL, thth = 0.01, constant = F, status = T, cores = 1)
```

**LDJump** is designed to estimate recombination rates from segments containing information (by SNPs). Therefore, the program checks all segments (based on the given segment lengths) for the number of SNPS. In case of segments without SNPs, the program will inform the user and ask for input providing the following two options: 
* "n": **LDJump** will be stopped and the user should retry with a larger segment length. 
* "y": **LDJump** will continue and impute the recombination rate of the segment without SNPs with a weighted mean of adjacent segments. 

In order to avoid the user input and accept that **LDJump** imputes recombination rates for these segments, we have added the parameter *accept* (by default *FALSE*), which should then be set to *TRUE*. 

We also included a logical parameter *constant* in **LDJump**, which is *FALSE* by default to estimate variable recombination rates. In the case that *constant* is set to *TRUE*, **LDJump** will provide a constant recombination rate estimator of the whole sequence. 

A logical parameter *rescale* enables to transform the sequence positions to the unit interval if set to *TRUE*.

A logical parameter *status*, which is *TRUE* by default, prints the current status of the calculated segment on screen or to the file *LDJump_Status.txt* in case of parallelization. 

An integer parameter *cores* enables to parallelize **LDJump**, where the parameter equals the number of cores on which **LDJump** should run. 

A logical parameter *demography*, which is *FALSE* by default, enables to estimate the recombination rates using a generalized additive regression model (GAM) which is based on simulated samples of populations undergoing demographic effects. 

We recommend to run **LDJump** from the same path where the sample file is located in order that all created temp files will be deleted after completion.
