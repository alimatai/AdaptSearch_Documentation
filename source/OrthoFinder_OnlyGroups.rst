**********************
OrthoFinder_OnlyGroups
**********************


Overview
========

OrthoFinder is a fast, accurate and comprehensive analysis tool for comparative genomics. It finds orthologues and orthogroups infers gene trees for all orthogroups and infers a rooted species tree for the species being analysed. OrthoFinder also provides comprehensive statistics for comparative genomic analyses. OrthoFinder is simple to use and all you need to run it is a set of protein sequence files (one per species) in FASTA format (Emms, D.M. and Kelly, S., 2015).

OrthoFinder_OnlyGroups makes available under Galaxy the first part of OrtohFinder, e.g Orthogroups inference.

User parameters
===============

This parameters list reports only the ones available under Galaxy. For a complete command-line OrthoFinder manual, see https://github.com/davidemms/OrthoFinder

Two types of inputs are possible: from **fasta proteomes** of from **blast pre-computed results** (blastp from by a previous OrthoFinder run)

 #. Inputs when using fasta proteomes :

    #. The list of fasta files

 #. Inputs when using blast results (files from the 'blast directory') :

    #. blastX_Y.txt files
    #. '_.fa' files
    #. SpeciesIds.txt file
    #. SequencesIDs.txt file

 #. The inflation parameter (clustering parameter)


References
==========

Publication : Emms, David M. and Kelly, Steven (2015). OrthoFinder: solving fundamental biases in whole genome comparisons dramatically improves orthogroup inference accuracy. In Genome Biology, 16 (1). [doi:10.1186/s13059-015-0721-2

Source code Available at https://github.com/davidemms/OrthoFinder

Back to `main page <index.html>`_.