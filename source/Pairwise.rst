********
Pairwise
********


Overview
========

This tool searches for different homologous genes (tblastx with RBH) from pairwise comparisons between a set of fasta files (one file per species). It returns lists of homologous genes pairs between each pair of species

User parameters
===============

 #. Fasta files (outputs of `Filter_Assemblies <Filter_Assemblies.html>`_) separated by commas
 #. The blast e-value
 #. The alignment tool to use : the user can choose between tblastx (sensitive and very slow) and diamond (very fast and apparently a bit less sensitive). 

Code documentation
==================

Algorithm
---------

For each possible couple of species :
 #. A first blast is computed, the species_1 being the query and the species_2 the database
 #. For each query, only the Best Hit is kept
 #. Intermediate fasta files are built with the best_hits from query and db
 #. A second blast is computed, the species_2 being the query and the recorded best_hit queries being the database
 #. Only the Reciprocical Best Hits are kept.
 
If diamond is used, a call to BioPython Bio.Seq and Bio.Alphabet is used to translate sequences in every reading frame. Every translation is made with the original sequence name, with the addition of the suffix 'orf_x', so it is easy to keep track of the original sequence name.

.. note:: Many temporary files are generated ; the scripts work with species and sub-directories names, which are transfered from one script to another. The involved code can be uneasy to read.

Source code
-----------

The tool consists in several scripts following each others, launched on each species couple iteration.

File: S01_run_first_blast.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is the main script, running the first blast and then launching sub-scripts (S02,03,04) on every iteration of species couples.

* The species_1 is the query
* The species_2 is the database

File: S02_04_keep_one_hit_from_blast.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This scripts is run twice on each species couple iteration : after each blast. Every best hit for every query sequence is recorded, then a temporary fasta file containing only those best hits is generated.

.. py:function:: list_with_max_score(list_of_hits)

   :param list_of_hits: a list of blast hits (each element being a line from a tab-formated blast output : query,db,score,evalue...). All queries are the same.
   :type list_of_hits: list   
   :return: the elem of the list which has the best score
   :rtype: 

File : S03_run_second_blast.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Runs the second blast (with the same alignment tool than for the 1st blast) between the best hits of the 1st blast and the input transcriptom

* The species_2 is the query
* The species_1 sequences who had a hit during the first blast are the database

File : S05_find_rbh.py
^^^^^^^^^^^^^^^^^^^^^^

This last script compare all the best hits from both blasts and keeps only reciprocical best hits.

References
==========

Blast Manual : https://www.ncbi.nlm.nih.gov/books/NBK279690/pdf/Bookshelf_NBK279690.pdf

Diamond publication : Benjamin Buchfink, Chao Xie & Daniel H. Huson, Fast and Sensitive Protein Alignment using DIAMOND, Nature Methods, 12, 59–60 (2015) doi:10.1038/nmeth.3176.

Diamond Manuel and source code : https://github.com/bbuchfink/diamond

Back to `main page <index.html>`_.
