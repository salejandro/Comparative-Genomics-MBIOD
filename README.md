<p align="left">
<img src="http://www.ub.edu/molevol/CG-Master/biodiversity.png"><img align="right" src="http://www.ub.edu/molevol/CG-Master/logo2.png" width="800" height="100">  
</p>

# Comparative Genomics

Instructor: **Alejandro Sánchez**  

</br>

## Introduction

The objective of this practice is to increase your understanding of the main concepts behind evolutionary inference by doing some practical work with a representation (just a few examples) of the state-of-the-art computational tools to estimate phylogenetic relationships between genes and genomes, and to determine the functional consequences of mutations in these sequences. This fundamental knowledge has great importance and applicability in genetic and biomedical research, and in biodiversity studies.

The biological model used in this practices are sarbecoviruses, and more specifically, a group of viruses closely related with SARS-Cov2 (the the type 2 coronavirus that causes severe acute respiratory syndrome) (part 1), and the BA.1 sublineage of the SARS-CoV2 variant of concern (VOC) Omicron (part 2).

</br>
</br>

<p align="center">
<img src="http://www.ub.edu/molevol/CG-MGG/sars2.png" width="900" heigh="900">
</p>

> **Fig. 1** Schematic diagrams of the SARS-CoV-2 virus particle (a) and genome (b). The genome encodes sixteen nonstructural proteins (nsp1–11, 12–16) and six accessory proteins. Plpro: papain like protease, 3CLPro: 3C-like proteinase, *__RdRp: RNA-dependent RNA polymerase__*, Hel:
Helicase, *__S: spike__*. Modified from Signal Transduction and Targeted Therapy (2021)6:233.

</br>

In the first part of this practice you will reconstruct the phylogenetic relationships of a group of sarbecoviruses using different genes. This analysis is very illustrative of the different evolutionary rates of viral genes, as well as of the mosaic nature of the genomes of viruses such as the SARS-CoV2, which originated after recombination between different strains of sarbecoviruses. In the second part, you will estimate the effect on virus fitness of the missense mutations (amino acid replacements) accumulated in the sublineage BA.1 of Omicron, and compare this effect with the estimated impact for the same positions prior to the emergence of this VOC (all SARS-CoV-2 near full-length genome sequences present in GISAID on 21/11/2021 that passed some quality control checks; [Martin et al. 2022](https://academic.oup.com/mbe/article/39/4/msac061/6553617)). This analysis in an example of how the accumulation of mutations with individual fitness costs but adaptive in combination with other mutations, could alter the function of a protein (Spike), while remained completely undetected despite unprecedented global genomic surveillance efforts.

</br>
</br>

<p align="center">
<img src="http://www.ub.edu/molevol/CG-MGG/omicronfreq.png" width="900" heigh="900">
</p>

> **Fig. 2** Frequency of the different SARS-CoV2 variants (colored by clade) at different time points as given in the Latest global SARS-CoV-2 analysis with GISAID data ([Nextstrain SARS-CoV-2 resources](https://nextstrain.org/ncov/gisaid/global/all-time); last accessed 16/11/2022). The sublineage BA.1 of Omicron correspond to the clade 21K.

---

## Unix based OS

If you are not familiarized with the terminal app in Unix based operating systems, please take a look at these introductory manuals:

   + [Linux](https://ubuntu.com/tutorials/command-line-for-beginners#1-overview)
   + [Mac](https://support.apple.com/en-in/guide/terminal/welcome/mac)

## Software and Data

Before starting the practice, confirm that you have installed `Docker` in your computer. (i) confirm that you have conda (or anaconda) (you can find system requirements and regular installation instructions [here](xxxxxxxxxxx). The reason for installing `Docker` and working within a container of a prebuilt image is to perform the computer lab in a stable environment with a specific `Python` version (3.9), as well as to avoid the complexity of software (iqtree, raxml-ng or hyphy) compilation on different operating systems with different configurations. 

**IMPORTANT WARNING: Many of the tools that will be used in this practice are not available for Windows operating systems, even when conda (anaconda) environment is installed. In general, bioinformatics programs for manipulating and analyzing genomic data are only available or tested for Linux and Mac. If you have a Windows based PC or laptop, **I strongy recommend to install a Linux distribution on a virtual machine (for example [wsl](https://learn.microsoft.com/en-us/windows/wsl/install)).

### Data files availability:

The genome sequences for this practice were retrieved either from [GenBank](https://www.ncbi.nlm.nih.gov/genbank/) or [GISAID](https://gisaid.org/) databases and correspond to:

1.  The genomic sequences of the 17 sarbecoviruses used for Figure 1 in [Temmam et al. (2022)](https://www.nature.com/articles/s41586-022-04532-4)

2.  A subset of 4717 high-quality genomes of the BA.1
variant (Omicron) collected world wide between 05/01/2020 and 22/06/2022. You can find the metadata associated with these genomes in the file BA.1.metadata, and the instructions for retrieve these sequences in the file [GISAID-BA.1-sequences.md](https://github.com/salejandro/Comparative-Genomics-MGG/blob/main/omicron_seqs.md).

The [FASTA](https://es.wikipedia.org/wiki/Formato_FASTA) files with the data decribed above will be in the folder "/data" of your home.

</br>

<p align="center">
<img src="http://www.ub.edu/molevol/CG-MGG/conda.jpeg" height="60">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="http://www.ub.edu/molevol/CG-MGG/python.png" height="60">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="http://www.ub.edu/molevol/CG-MGG/mafft.png" height="40">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="http://www.ub.edu/molevol/CG-MGG/iqtree-logo.svg" height="70">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="http://www.ub.edu/molevol/CG-MGG/raxmlng.png" height="40">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="http://www.ub.edu/molevol/CG-MGG/logo.svg" height="70">
</p>

</br>


---

## Part 1. Phylogenetic tree

Phylogenomics aims at establishing the evolutionary relationships between organisms using genome data. However, not all parts of a genome evolve at the same rate or completely independently since their split from a common ancestor, due to, for example, past recombination events or strong positive selection acting deferentially on particular genes and lineages. These scenarios may cause phylogenies to reflect different histories across the genome. Sarbecoviruses genomes, including SARS-CoV2, are not an exception. At least 15 recombination fragments have been identified when comparing most relevant sarbecoviruses. In fact, SARS-CoV-2 show recombination events from different donor sarbecoviruses strains, some of them spanning functionally important domains of the protein Spike, which point to their putative adaptive nature.

</br>
</br>

<p align="center">
<img src="http://www.ub.edu/molevol/CG-MGG/rec.png" width="900">
</p>

> **Fig. 3.** Recombination events estimated in the evolutionary history of sarbecoviruses. Modified from [Temmam et al. (2022)](https://www.nature.com/articles/s41586-022-04532-4).

</br>

To illustrate the different evolutionary history of some viral genome regions, you will conduct a ML phylogenetic analysis of the nucleotide sequences of two recombination fragments: the fragment that encodes, among others, the RNA-dependent RNA polymerase (RdRp) and the Helicase of the virus (fragment 7 in Fig. 3), and the fragment that encodes most of the S-gene, including the Receptor Binding Domain, RBD (fragment 11 in Fig.3), of representative human, bat, and pangolin sarbecoviruses. These two fragments encode proteins with very different functions, which is clearly reflected in their contrasting evolutionary rates.

### Workflow:

1.  First, you have to trim down sarbecoviruses genomic sequences to the recombination fragment neighborhood using a `Python` script:

      ```bash
      FILE="sarbecoviruses.fasta" # rename accordingly
      
      # Fragment 7:
      python3.9 scripts/filter-sites.py data/$FILE  12000,18000 > ${FILE}.f7.fasta
   
      # Fragment 11:
      python3.9 scripts/filter-sites.py data/$FILE  22000,25000 > ${FILE}.f11.fasta
      
      ``` 
 </br>
 
2.  Now, you can build a multiple sequence alignment for each fragment using `mafft`:
   
      ```bash
      mafft ${FILE}.f7.fasta > ${FILE}.f7.msa
      mafft ${FILE}.f11.fasta > ${FILE}.f11.msa
      
      ``` 
 </br>
 
3.  You will use `iqtree` to obtain ML phylogenetic trees based on the nucleotide sequences from these regions:

      ```bash
      iqtree -s ${FILE}.f7.msa -m GTR+I+G4+F -bb 1000
      iqtree -s ${FILE}.f11.msa -m JC+F -bb 1000
      
      ``` 
      > To visualize the trees you can use the application [figtree](https://github.com/rambaut/figtree/releases). If you are running Ubuntu in the Windows Subsystem for Linux (WSL), download and execute `figtree` in your Windows system. You can find the tree files generated by the above commands in the Ubuntu folder. To find the Ubuntu folder, type `\\wsl$` in the navigation bar of your File Explorer (the Ubuntu system must be running!).

 </br>

4. For comparison purposes, you will also built a tree based on a protein sequence aligment of the Receptor Binding Domain (RBD) of Spike. This poorly conserved across sarbecoviruses region is part of the protein Spike and is the domain that binds ACE2 receptors to entry into human cells:

   + To trim down Sarbecovirues sequences to the RBD neighborhood using a `Python` script:
      
      ```bash
      python3.9 scripts/filter-sites.py data/${FILE} 22000,24000 > ${FILE}.RBD.raw
      
      ```
   + To map trimmed Sarbecovirus sequences to the RBD nucleotide sequences of the SARS-CoV2 reference ([NC_045512](https://www.ncbi.nlm.nih.gov/nuccore/NC_045512)), using a codon alignment algorithm:
   
      ```bash
      REF="data/RBD.reference"
      bealign -r ${REF} ${FILE}.RBD.raw ${FILE}.RBD.bam   
      bam2msa ${FILE}.RBD.bam ${FILE}.RBD.msa 
      
      ```
      > This step allows to delimit the alignment to only the coding region of RBD. The output of bealing is in [BAM format](https://samtools.github.io/hts-specs/SAMv1.pdf). The tool `bam2msa`converts the BAM file to FASTA format
   
   + To translate coding sequences to amino acid sequences:
         
      ```bash 
      transeq -sequence $FILE.RBD.msa -outseq ${FILE}.RBD.prot 
      
      ```
   
   + To align RBD protein sequences:
   
      ```bash
      mafft $FILE.RBD.prot > $FILE.RBD.prot.align   
      
      ```
   
   + To obtain the ML phylogenetic trees of the RBD protein region of sarbecoviruses:
   
      ```bash
     # 1:
     iqtree -s sarbecoviruses.fasta.RBD.prot.align -st AA -m LG4M+G4 -bb 1000
     # 2:
     cp sarbecoviruses.fasta.RBD.prot.align sarbecoviruses.fasta.RBD.prot.align_dup
     iqtree -s sarbecoviruses.fasta.RBD.prot.align_dup -st AA -m TEST -bb 1000
     
     ```
---

## Part 2. Analysis of selection in the BA.1 sublineage

In the second part of the practice, we are particularly interested in identifying Spike amino acids (codon sites) displaying evolutionary patterns that differed between Omicron sublineage BA.1 and other SARS-CoV-2 lineages that circulated prior to the emergence of Omicron. The rationale of this analysis is to investigate how could so many amino acid changes that adaptively alter the function of Spike accumulate at these sites, without being detected despite unprecedented global genomic surveillance efforts. You will first apply the codon substitution models implemented in the `hyphy` package to estimate functional constraints acting on non-synonymous sites of this gene after the emergence of Omicron, and then, you will compare your results with those from same analyses on non-Omicron genomes (Table 1).

##### **Table 1**. Frequencies in non-Omicron SARS-CoV-2 genomes of some Spike amino acid mutations ([Martin et al. 2022](https://academic.oup.com/mbe/article/39/4/msac061/6553617)). Since the length of the Spike protein is highly conserved across the BA.1 sequences, the numbering of the positions in the table will match that of the alignment that your will use in this practice

<p align="center">
<img src="http://www.ub.edu/molevol/CG-MGG/table1.png" width="800">
</p>

</br>

### Workflow:

1.  You have first to trim down genomic sequences to the S-gene neighborhood using a `Python` script:

      ```bash
      FILE="omicron-BA1.fasta" # rename accordingly
      python3.9 scripts/filter-sites.py data/$FILE  20000,26000 > ${FILE}.S.raw
      
      ``` 
   > You trim sequences to this wide range be sure to include the whole S-gene of all the sequences
   
</br>

2.  Then, you will use `bealign` to align trimmed Sarbecovirus sequences to the S-gene of the SARS-CoV2 reference ([NC_045512](https://www.ncbi.nlm.nih.gov/nuccore/NC_045512)), using a codon alignment algorithm:

      ```bash
      bealign -r CoV2-S ${FILE}.S.raw ${FILE}.S.bam
      bam2msa ${FILE}.S.bam ${FILE}.S.msa
      
      ```
      > This step allows to delimit the alignment to only the coding region of the S-gene (which is already available as an option for -r in `bealign`)

</br>

3.  Because selection analyses gain no or minimal power from including identical or very similar sequences, you will filter BA.1 sequences using pairwise genetic distances. To do that, you will use some `Python` scripts and the program `tn93`:

      + To identify and remove all identical sequences up to ambiguous nucleotides:
         ```bash
         python3.9 scripts/exact-copies.py  ${FILE}.S.msa > ${FILE}.u.clusters.json
         python3.9 scripts/cluster-processor.py ${FILE}.u.clusters.json > ${FILE}.S.u.fas
         
         ```

      + To identify and remove all remaining sequences that are within t= 0.0 ([Tamura-Nei 93](https://pubmed.ncbi.nlm.nih.gov/8336541/) distance) genetic distance:
         ```bash
         tn93-cluster -t 0.0 ${FILE}.S.u.fas > ${FILE}.t0.clusters.json
         python3.9 scripts/cluster-processor.py ${FILE}.t0.clusters.json > ${FILE}.S.all.fas
         
         ```

      + To reduce the set of sequences to clusters that are all within t=0.00075 distance of one another:
         ```bash
         tn93-cluster -t 0.00075 ${FILE}.S.all.fas > ${FILE}.t1.clusters.json
         python3.9 scripts/cluster-processor.py ${FILE}.t1.clusters.json > ${FILE}.S.uniq.fas
         
         ```

      + To identify and remove outliers, low quality or misclassified sequences that are t=0.002 away from the gererated clusters:

         ```bash
         tn93-cluster -t 0.002 ${FILE}.S.uniq.fas > ${FILE}.t2.clusters.json
         python3.9 scripts/cluster-processor.py ${FILE}.t1.clusters.json ${FILE}.t2.clusters.json > ${FILE}.S.uniq-all.fas 2> ${FILE}.S.blacklist.txt
         
         ```
   
      + Finally, to build a majority consensus for each remaining cluster and remove clusters that comprise fewer than 3 sequences:
         ```bash
         python3.9 scripts/cluster-processor-consensus.py 3 ${FILE}.S.msa ${FILE}.S.uniq-all.fas ${FILE}.t1.clusters.json ${FILE}.t0.clusters.json ${FILE}.u.clusters.json > ${FILE}.S.uniq.fas
         
         ```
   
 </br>

4.  The program hyphy estimates synonymous and non-synonymous substitution rates in a maximum likelihood (ML) phylogetic framework using different (complementary) [methods](https://www.hyphy.org/methods/selection-methods/). Hence, first of all, you need a phylogenetic tree with the relationships among the Spike sequences retained in the previous step (after comprising to unique haplotypes). In this case, you will use the program `raxml-ng`: 

      ```bash
      threads=4
      raxml-ng --redo --threads $threads --msa ${FILE}.S.uniq.fas --tree pars{5} --model GTR+G+I
      cp ${FILE}.S.uniq.fas.raxml.bestTree ${FILE}.S.uniq.tree
      
      ```
 
 </br>

5. Once you have the tree, you are ready to run a selection analysis with different methods implemented in the software hyhpy. For this practice, you will run [BUSTED](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/msv035), [FEL](https://academic.oup.com/mbe/article/22/5/1208/1066893), [MEME](https://journals.plos.org/plosgenetics/article?id=10.1371/journal.pgen.1002764) and [BGM](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.0030231) analyses:</br>

   +  The first method is **BUSTED** (**B**ranch-Site **U**nrestricted **S**tatistical **T**est for **E**pisodic **D**iversification). By applying this method, you are asking whether the S-gene has experienced **positive selection at at least one site on at least one branch** of the tree (=one BA.1 genome):

      ```bash
      hyphy busted --alignment ${FILE}.S.uniq.fas --tree ${FILE}.S.uniq.tree --branches Internal --rates 3 --starting-points 5
      
      ```
   
   + When applying **FEL** (**F**ixed **E**ffects **L**ikelihood) to your data, you obtain the ML estimate of non-synoymous (dN) and synonymous (dS) substitution rates in each codon site (amino acid position) for the entire Spike CDS alignment acroos the phylogeny. Therefore, you are assuming **constant selection pressures along the entire history of BA.1 sequences**:
   
      ```bash
      HYPHYMPI="mpirun -np $threads HYPHYMPI"
      $HYPHYMPI fel --alignment ${FILE}.S.uniq.fas  --tree ${FILE}.S.uniq.tree --branches Internal  --ci Yes
      
      ```
   
   + Whith **MEME** (**M**ixed **E**ffects **M**odel of **E**volution) you will identify episodes of **positive selection** in the S-gene affecting a **small subset of branches at individual sites**. In other words, episodic positive or diversifying selection:
   
      ```bash
      $HYPHYMPI meme --alignment ${FILE}.S.uniq.fas  --tree ${FILE}.S.uniq.tree --branches Internal
      
      ```
        > Notice that in the two last models (FEL and MEME) you use the MPI (Message Passing Interface) version of the program hyphy. This version allows multiprocessing (parallel programing), which is very helpful when running computational intensive models  
     
   + Finally, the **BGM** (**B**ayesian **G**raphical **M**odel) method is a tool for detecting correlated amino acid substitutions in the Spike protein of Omicron BA.1. This correlation should be suggestive of **coevolutionary interactions between amino acid positions** in this protein:
     
      ```bash
      hyphy bgm --alignment ${FILE}.S.uniq.fas --tree ${FILE}.S.uniq.tree --branches Internal --min-subs 2 --steps 1000000 --samples 1000 --burn-in 100000
      
      ```

To visualize json results use [hyphy-vision tool](http://vision.hyphy.org/)

***

<p><strong>Doubts?</strong></p>

Contact M. Riutort: mriutort(at)ub.edu  
Contact A. Sánchez: elsanchez(at)ub.edu 


