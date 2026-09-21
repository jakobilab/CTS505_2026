# Introduction to R: day-to-day data wrangling and biomart

## loading, subsetting, and merging data frames

```R

gene1 <- read.table("genes1.tsv", header = T, sep = "\t")
gene2 <- read.table("genes2.tsv", header = T, sep = "\t")


```




## install biomart

```R

# install bioconductor first
if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install(version = "3.17")

# now install biomart
BiocManager::install("biomaRt")

# load biomart

# does not work
library(biomart)

# load correctly
library(biomaRt)



```


## work with biomart

```R

# set up annotation
annotation <- "rnorvegicus_gene_ensembl"

# set up mart object
ensembl <- useMart("ensembl", dataset = annotation)

# query data
result <- getBM(attributes = c("external_gene_name", "ensembl_gene_id", "description"), values = names(go_background_blank), mart = ensembl)

```


library(biomaRt)

# Connect to the Ensembl BioMart
ensembl <- useEnsembl(biomart = "genes", dataset = "hsapiens_gene_ensembl")

# Explore available attributes and filters
listAttributes(ensembl)[1:10, ]
listFilters(ensembl)[1:10, ]


# Gene ID Conversion (Ensembl ↔ Gene Symbol ↔ Entrez)
genes <- c("BRCA1", "TP53", "EGFR")

getBM(attributes = c("hgnc_symbol", "ensembl_gene_id", "entrezgene_id"),
      filters = "hgnc_symbol",
      values = genes,
      mart = ensembl)

# Section 3: Retrieve Gene Coordinates      
getBM(attributes = c("hgnc_symbol", "chromosome_name", "start_position", "end_position", "strand"),
      filters = "hgnc_symbol",
      values = c("BRCA1", "TP53"),
      mart = ensembl)

      
# Section 4: Annotation of RNA-seq Results
      
de_genes <- c("ENSG00000141510", "ENSG00000012048", "ENSG00000146648")

annot <- getBM(attributes = c("ensembl_gene_id", "hgnc_symbol", "description", "gene_biotype"),
               filters = "ensembl_gene_id",
               values = de_genes,
               mart = ensembl)

      
# Section 5: Cross-Species Ortholog Mapping
      
mouse <- useEnsembl(biomart = "genes", dataset = "mmusculus_gene_ensembl")

orth <- getLDS(attributes = c("mgi_symbol", "ensembl_gene_id"),
               filters = "mgi_symbol",
               values = c("Trp53", "Brca1"),
               mart = mouse,
               attributesL = c("hgnc_symbol", "ensembl_gene_id"),
               martL = ensembl)
