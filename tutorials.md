---
layout: tutorial_hands_on

title: "Analyses of metatranscriptomics data -ASAIM"
zenodo_link: ""
questions:
  - "How to analyze metatranscriptomics data?"
  - "What information can be extracted of metatranscriptomics data?"
  - " How to assign taxa and function to the identified sequences?"
objectives:
  - "Choosing the best approach to analyze metatranscriptomics data"
  - "Visualisation of a community structure"
time_estimation: "4H"
key_points:
  - "With shotgun data, we can extract information about the studied community structure and also the functions realised by the community"
  - "Metatranscriptomics data analyses are complex and time-consuming"
contributors:
  - subinamehta
  - emmaleith
  - pratikdjagtap
  - praveenkuman
  - jrsajulga
  - timgriffin
  - shiltemann
  - bebatut
---

# Introduction
{:.no_toc}



> ### Agenda
>
> In this tutorial, we will deal with:
>
> 1. TOC
> {:toc}
>
{: .agenda}


## Importing the data






> ### {% icon hands_on %} Hands-on: Data upload
>
> 1. Import from [Zenodo](https://zenodo.org/record/815875) or from the data library (in "Analyses of metagenomics data") the files
>    - `SRR531818_pampa.fasta`
>    - `SRR651839_anguil.fasta`
>
>    ```
>    https://zenodo.org/record/815875/files/SRR531818_pampa.fasta
>    https://zenodo.org/record/815875/files/SRR651839_anguil.fasta
>    ```
>
>    > ### {% icon tip %} Tip: Importing data via links
>    >
>    > * Copy the link location
>    > * Open the Galaxy Upload Manager
>    > * Select **Paste/Fetch Data**
>    > * Paste the link into the text field
>    > * Press **Start**
>    {: .tip}
>
>    > ### {% icon tip %} Tip: Importing data from a data library
>    >
>    > * Go into "Shared data" (top panel) then "Data libraries"
>    > * Click on "Training data" and then "Analyses of metagenomics data"
>    > * Select interesting file
>    > * Click on "Import selected datasets into history"
>    > * Import in a new history
>    {: .tip}
>
>    As default, Galaxy takes the link as name, so rename them.
>
{: .hands_on}

In the previous section, we saw how to analyze amplicon data to extract the community structure. Such information can also be extracted from shotgun metagenomic data.

In shotgun data, full genomes of the micro-organisms in the environment are sequenced (not only the 16S or 18S). We can then have access to the rRNA (only a small part of the genomes), but also to the other genes of the micro-organisms. Using this information, we can try to answer questions such as "What are the micro-organisms doing?" in addition to the question "What micro-organisms are present?".

In this second part, we will use a metagenomic sample of the Pampas Soil ([SRR606451](https://www.ebi.ac.uk/metagenomics/projects/SRP016633/samples/SRS372043/runs/SRR606451/results/versions/2.0)).

## Data upload

> ### {% icon hands_on %} Hands-on: Data upload
>
> 1. Create a new history
> 2. Import the `SRR606451_pampa` Fasta file from [Zenodo](http://zenodo.org/record/815875) or from the data library (in "Analyses of metagenomics data")
>
>    ```
>    https://zenodo.org/record/815875/files/SRR606451_pampa.fasta
>    ```
>
{: .hands_on}
**Trim Galore!** {% icon tool %} with the following parameters:
>   - {% icon param-select %} *”Is this library paired- or single-ended?”*: ‘Paired-end’
>   - {% icon param-file %} *”Reads in FASTQ format”*: ‘FASTQ of read 1‘
>   - {% icon param-file %} *”Reads in FASTQ format”*: ‘FASTQ of read 2’
>   - {% icon param-select %} *”Adapter sequence to be trimmed”*: ‘Automatic detection’
>   - {% icon param-check %} *”Trims 1 bp off every read from its 3’ end”*: ‘No’
>   - {% icon param-text %} *”Remove N bp from the 3’ end of read 1”*: ‘’
>   - {% icon param-text %} *”Remove N bp from the 3’ end of read 2”*: ‘’
>   - {% icon param-select %} *”Trim Galore! Advanced settings”*: ‘Use defaults’
>   - {% icon param-select %} *”RRBS specific settings”*: ‘Use defaults (no RRBS)’

**Filter with SortMeRNA** {% icon tool %} with the following parameters:
>   - {% icon param-select %} *”Sequencing type”*: ‘Reads are paired’
>   - {% icon param-file %} *”Forward reads”*: ‘Read 1: trimmed’
>   - {% icon param-file %} *”Reverse reads”*: ‘Read 2: trimmed’
>   - {% icon param-check %} *”If one of the paired-end reads aligns and the other one does not”*: ‘Output both reads to rejected file (--paired_out)’
>   - {% icon param-select %} *”Which strands to search”*: ‘Search both strands’
>   - {% icon param-select %} *”Databases to query”*: ‘Public pre-indexed ribosomal databases’
>   - {% icon param-check %} *”rRNA databases”*: ‘Select all’
>   - {% icon param-select %} *”Include aligned reads in FASTA/FASTQ format?”*: ‘Yes (--fastx)’
>   - {% icon param-check %} *”Include rejected reads file?”*: ‘Yes’
>   - {% icon param-check %} *”Generate statistics file”*: ‘No’
>   - {% icon param-select %} *”Alignment report”*: ‘Do not report alignments’
>   - {% icon param-text %} *”E-value threshold”*: ‘1’
>   - {% icon param-text %} *”SW score for a match”*: ‘2’
>   - {% icon param-text %} *”SW penalty for a mismatch”*: ‘-3’
>   - {% icon param-text %} *”SW penalty for introducing a gap”*: ‘5’
>   - {% icon param-text %} *”SW penalty for extending a gap”*: ‘2’
>   - {% icon param-text %} *”SW penalty for ambiguous letters (N’s)”*: ‘-3’

**FASTQ interlacer** {% icon tool %} with the following parameters:
>   - {% icon param-select %} *”Type of paired-end datasets”*: ‘2 separate datasets’
>   - {% icon param-file %} *”Left-hand mates”*: ‘File’
>   - {% icon param-file %} *”Right-hand mates”*: ‘File’

## Extraction of taxonomic information

As for amplicon data, we can extract taxonomic and community structure information from shotgun data. Different approaches can be used:

- Same approach as for amplicon data with identification and classification of OTUs

    Such an approach requires a first step of sequence sorting to extract only the 16S and 18S sequences, then using the same tools as for amplicon data. However, rRNA sequences represent a low proportion (< 1%) of the shotgun sequences so such an approach is not the most statistically supported

- Assignation of taxonomy on the whole sequences using databases with marker genes

In this tutorial, we use the second approach with MetaPhlAn2. This tools is using a database of ~1M unique clade-specific marker genes (not only the rRNA genes) identified from ~17,000 reference (bacterial, archeal, viral and eukaryotic) genomes.

> ### {% icon hands_on %} Hands-on: Taxonomic assignation with MetaPhlAn2
>
> 1. **MetaPhlAN2** {% icon tool %} with
>    - "Input file" to the imported file
>    - "Database with clade-specific marker genes" to `locally cached`
>    - "Cached database with clade-specific marker genes" to `MetaPhlAn2 clade-specific marker genes`
>
> This step may take a couple of minutes.
{: .hands_on}

3 files are generated:

- A tabular file with the community structure

    ```
    #SampleID   Metaphlan2_Analysis
    k__Bacteria 100.0
    k__Bacteria|p__Proteobacteria   86.20712
    k__Bacteria|p__Actinobacteria   13.79288
    k__Bacteria|p__Proteobacteria|c__Gammaproteobacteria    86.20712
    k__Bacteria|p__Actinobacteria|c__Actinobacteria 13.79288
    ```

    Each line contains a taxa and its relative abundance found for our sample. The file starts with high level taxa (kingdom: `k__`) and go to more precise taxa.


- A BIOM file with the same information as the previous file but in BIOM format

    It can be used then by mothur and other tools requiring community structure information in BIOM format

- A SAM file with the results of the mapping of the sequences on the reference database

> ### {% icon question %} Questions
>
> 1. What is the most precise level we have access to with MetaPhlAn2?
> 2. What are the two orders found in our sample?
> 3. What is the most abundant family in our sample?
>
> > ### {% icon solution %} Solution
> > 1. We have access to species level
> > 2. Pseudomonadales and Solirubrobacterales are found in our sample
> > 3. The most abundant family is Pseudomonadaceae with 86.21 % of the assigned sequences
> {: .solution }
{: .question}

Even if the output of MetaPhlAn2 is bit easier to parse than the BIOM file, we want to visualize and explore the community structure with KRONA

> ### {% icon hands_on %} Hands-on: Interactive visualization with KRO
**MetaPhlAn2** {% icon tool %} with the following parameters:
>   - {% icon param-tool %} *”Input file”*: ‘FASTQ interlacer output’
>   - {% icon param-select %} *”Database with clade-specific marker genes”*: ‘Locally cached’
>   - {% icon param-select %} *”Cached database with clade-specific marker genes”*: ‘MetaPhlAn2 clade-specific marker genes’
>   - {% icon param-select %} *”Type of analysis to perform”*: ‘Profiling a metagenomes in terms of relative abundances’
>   - {% icon param-select %} *”Taxonomic level for the relative abundance output”*: ‘All taxonomic levels’
>   - {% icon param-text %} *”Minimum total nucleotide length for the markers in a clade for estimating the abundance without considering sub-clade abundances”*: ‘2000’
>   - {% icon param-text %} *”Sam records for aligned reads with the longest subalignment length smaller than this threshold will be discarded”*: ‘0’
>   - {% icon param-check %} *”Profile viral organisms?”*: ‘Yes’
>   - {% icon param-check %} *”Profile eukaryotic organisms?”*: ‘Yes’
>   - {% icon param-check %} *”Profile bacteria organisms?”*: ‘Yes’
>   - {% icon param-check %} *”Profile archea organisms?”*: ‘Yes’
>   - {% icon param-text %} *”Quantile value for the robust average”*: ‘0.1’
## Extraction of functional information

We would now like to answer the question "What are the micro-organisms doing?" or "Which functions are performed by the micro-organisms in the environment?".

In the shotgun data, we have access to the gene sequences from the full genome. We use that to identify the genes, associate them with a function, build pathways, etc., to investigate the functional part of the community.

> ### {% icon hands_on %} Hands-on: Metabolism function identification
>
> 1. **HUMAnN2** {% icon tool %} with
>    - "Input sequence file" to the imported sequence file
>    - "Use of a custom taxonomic profile" to `Yes`
>    - "Taxonomic profile file" to `Community profile` output of `MetaPhlAn2`
>    - "Nucleotide database" to `Locally cached`
>    - "Nucleotide database" to `Full`
>    - "Protein database" to `Locally cached`
>    - "Protein database" to `Full UniRef50`
>    - "Search for uniref50 or uniref90 gene families?" to `uniref50`
>    - "Database to use for pathway computations" to `MetaCyc`
>    - "Advanced Options"
>    - "Remove stratification from output" to `Yes`
>
>    This step is long so we generated the output for you!
>
> 2. Import the 3 files whose the name is starting with "humann2"
>
>    ```
>    https://zenodo.org/record/815875/files/humann2_gene_families_abundance.tsv
>    https://zenodo.org/record/815875/files/humann2_pathways_abundance.tsv
>    https://zenodo.org/record/815875/files/humann2_pathways_coverage.tsv
>    ```
{: .hands_on}

HUMAnN2 generates 3 files

- A file with the abundance of gene families

    Gene family abundance is reported in RPK (reads per kilobase) units to normalize for gene length. It reflects the relative gene (or transcript) copy number in the community.

    The "UNMAPPED" value is the total number of reads which remain unmapped after both alignment steps (nucleotide and translated search). Since other gene features in the table are quantified in RPK units, "UNMAPPED" can be interpreted as a single unknown gene of length 1 kilobase recruiting all reads that failed to map to known sequences.

- A file with the coverage of pathways

    Pathway coverage provides an alternative description of the presence (1) and absence (0) of pathways in a community, independent of their quantitative abundance.

- A file with the abundance of pathways

> ### {% icon question %} Questions
>
> How many gene families and pathways have been identified?
>
> > ### {% icon solution %} Solution
> > 44 gene families but no pathways are identified
> {: .solution }
{: .question}

The RPK for the gene families are quite difficult to interpret in term of relative abundance. We decide then to normalize the values

**HUMAnN2** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input sequence file”*: ‘FASTQ interlacer joined output’
>   - {% icon param-select %} *”Use a custom taxonomic profile”*: ‘Yes’
>   - {% icon param-file %} *”Taxonomic profile file”*: ‘MetaPhlAn Community file’
>   - {% icon param-select %} *”Nucleotide database”*: ‘Full’
>   - {% icon param-select %} *”Software to use for translated alignment”*: ‘Diamond’
>   - {% icon param-select %} *”Protein database”*: ‘Locally cached’
>   - {% icon param-select %} *”Protein database”*: ‘Full UniRef50’
>   - {% icon param-select %} *”Search for uniref50 or uniref90 gene families?”*: ‘uniref50’
>   - {% icon param-select %} *”Database to use for pathway computations”*: ‘MetaCyc’

**Format MetaPhlAn2 output** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input file”*: ‘MetaPhlAn2 Community file’

**Export to GraPhlAn** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input file”*: ‘MetaPhlAn2 Community file’
>   - {% icon param-select %} *”Use a LEfSe output file as input?”*: ‘No’
>   - {% icon param-text %} *”List which levels should be annotated in the tree”*: ‘’
>   - {% icon param-text %} *”List which levels should use the external legend for the annotation”*: ‘’
>   - {% icon param-text %} *”List which levels should be highlight with a shaded background”*: ‘’
>   - {% icon param-text %} *”List which of the clades that should be highlight with a shaded background”*: ‘’
>   - {% icon param-text %} *”List of color to use for the shaded background”*: ‘’
>   - {% icon param-text %} *”Title of the GraPhlAn plot”*: ‘’
>   - {% icon param-text %} *”Title font size”*: ‘15’
>   - {% icon param-text %} *”Default size  for clades that are not found as biomarkers”*: ‘10’
>   - {% icon param-text %} *”Minimum value of clades that are biomarkers”*: ‘20’
>   - {% icon param-text %} *”Maximum value of clades that are biomarkers”*: ‘200’
>   - {% icon param-text %} *”Default font size”*: ‘10’
>   - {% icon param-text %} *”Minimum font size”*: ‘8’
>   - {% icon param-text %} *”Maximum font size”*: ‘12’
>   - {% icon param-text %} *”Font size for the annotation legend”*: ‘10’
>   - {% icon param-text %} *”Minimum abundance value for a clade to be annotated”*: ‘20.0’
>   - {% icon param-text %} *”Number of clades to highlight”*: ‘’
>   - {% icon param-text %} *”Minimum number of biomarkers to extract”*: ‘’
>   - {% icon param-text %} *”Row number containing the names of the features”*: ‘0’
>   - {% icon param-text %} *”Row containing the names of the samples”*: ‘0’
>   - {% icon param-text %} *”Row number to use as metadata”*: ‘’
>   - {% icon param-text %} *”Row number to skip from the input file”*: ‘’
>   - {% icon param-text %} *”Percentile of sample value distribution for sample selection”*: ‘’
>   - {% icon param-text %} *”Percentile of feature value distribution for sample selection”*: ‘’
>   - {% icon param-text %} *”Number of top samples to select”*: ‘’
>   - {% icon param-text %} *”Number of top features to select”*: ‘’

**Format MetaPhlAn2 output** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input file (MetaPhlAN2 output)”*: ‘MetaPhlAn2 Community File’

**Group abundances** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”HUMAnN2 output with UniRef 50 gene family abundance”*: ‘HUMAnN2 Gene families and their abundance file’
>   - {% icon param-check %} *”Use a custom Gene Ontology file?”*: ‘No’
>   - {% icon param-check %} *”Use a custom slim Gene Ontology file?”*: ‘No’
>   - {% icon param-check %} *”Use a custom correspondence between UniReg50 and precise GO?”*: ‘No’   

**Create a genus level gene families file** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Gene families input table”*: ‘HUMAnN2 Gene families and their abundance file’

**Combine MetaPhlAn2 and HUMAnN2 outputs** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input file corresponding to MetaPhlAN2 output”*: ‘MetaPhlAn2 Community File’
>   - {% icon param-file %} *”Input file corresponding HUMAnN2 output”*: ‘HUMAnN2 Gene families and their abundance file’
>   - {% icon param-select %} *”Type of characteristics in HUMAnN2 file”*: ‘Gene families’
 **Combine MetaPhlAn2 and HUMAnN2 outputs** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input file corresponding to MetaPhlAN2 output”*: ‘MetaPhlAn2 Community File’
>   - {% icon param-file %} *”Input file corresponding HUMAnN2 output”*: ‘HUMAnN2 Pathways and their abundance file’
>   - {% icon param-select %} *”Type of characteristics in HUMAnN2 file”*: ‘Gene families’

 **Generation, personalization and annotation of tree** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input tree”*: ‘Export to GraPhlAn Tree output’
>   - {% icon param-file %} *”Annotation file”*: ‘Export to GraPhlAn Annotation output’

 **Krona Pie chart** {% icon tool %} with the following parameters:
>   - {% icon param-select %} *”What is the type of your input data”*: ‘Tabular’
>   - {% icon param-file %} *”Input file”*: ‘Format MetaPhlAn2 Krona output’
>   - {% icon param-text %} *”Provide a name for the basal rank”*: ‘Root’
>   - {% icon param-check %} *”Combine data from multiple datasets”*: ‘No’

**GraPhlAn** {% icon tool %} with the following parameters:
>   - {% icon param-file %} *”Input tree”*: ‘Generation, personalization and annotation of tree output’
>   - {% icon param-select %} *”Output format”*: ‘PNG’
>   - {% icon param-text %} *”Dpi of the output image”*: ‘’
>   - {% icon param-text %} *”Size of the output image”*: ‘7’
>   - {% icon param-text %} *”Distance between the most external graphical element and the border of the image”*: ‘’



With the previous analyses, we investigate "Which micro-organims are present in my sample?" and "What function are performed by the micro-organisms in my sample?". We can go further in these analyses (for example, with a combination of functional and taxonomic results). We did not detail that in this tutorial but you can find more analyses in our tutorials on shotgun metagenomic data analyses.

# Conclusion
{:.no_toc}

We can summarize the analyses with amplicon and shotgun metagenomic data:

![Scheme to sum up the analysis](../../images/general-tutorial-scheme.png)

Both analyses are quite complex! However, in this tutorial, we only showed simple cases of metagenomics data analysis with subset of real data.

Check our other tutorials to learn more in detail of how to analyze metagenomics data.
