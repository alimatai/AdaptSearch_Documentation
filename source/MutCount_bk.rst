********
MutCount
********


Overview
********

This script counts the number of codons, amino acids, and types of amino acids in sequences, as well as the mutation bias from one item to another between 2 sequences. Counting is then compared to empirical p-values, obtained from bootstrapped sequences obtained from a subset of sequences.

In the output files, the pvalues indicate the position of the observed data in a distribution of empirical countings obtained from a resample of the data. Values above 0.95 indicate a significantly higher counting, values under 0.05 a significantly lower counting.

The script resamples random pairs of aligned codon to determine what countings can be expected under the hypothesis of an homogenous dataset. Countings are performed on each generated random alignement, thousands of alignments allow to draw a gaussian distribution of the countings. Then the script simply checks whether the observed data are within the 5% lowest or 5% highest values of the distribution.

User parameters
===============

Two modes available :
 #. Separated
 #. Concatenated

Separated mode
--------------

 #. Inputs are all fasta files from `CDS_Search <CDS_Search.html>`_ separated by commas
 #. Concatenated file from `ConcatPhyl <ConcatPhyl.html>`_ is used to retrieve all species names

Concatenated mode
-----------------

 #. Input fasta file is the 'Phylogeny_concatenation_fasta_nuc' from `ConcatPhyl <ConcatPhyl.html>`_
 #. List of species for countings : the user can focus countings on these species only
 #. List of species used for resampling : the user can focus the resampling on these species only
 #. Number of sampled codons : sets the length (in codons) of the resampled sequences
 #. Number of iterations : sets the number of resampled sequences


Code documentation
******************

Algorithm
=========

Separated mode :
----------------

.. todo:: Algo !

Concatenated mode :
-------------------

A whole bunch of dictionaries are used to store, for each sequences/pairs of sequences :
 #. Each codons/amino-acids/amino-acids types occurrences countings/frequences (3*2=6 dictionaries)
 #. Each codons/amino-acids/amino-acids types transitions countings/frequences (3*2=6 dictionaries)

.. code-block:: python

   # Example
   codons_counts_species_X = {'aaa':0, 'aac':0, ...}
   codons_transitions_counts_species_X_to_species_Y = {'aaa':{'aaa':0, 'aac':0, ...}, 
                                                       'aac':{'aaa':0, 'aac':0, ...}, 
                                                       ...}

Resampling is done on all or on a subset of sequences by picking codons at random positions. The more resemaplin


Then, Final dictionaries gathers all occurrences and transitions of all sequences, which are written to csv files using pandas.

.. code-block:: python

   # Example
   codons_counts_final = {'Species1': {'aaa':20, 'aac':12, ...},
                          'Species2': {'aaa':14, 'aac':17, ...},
                          ...}
   codons__transitions_counts_final = {'species_X_to_species_Y': {'aaa':{'aaa':12, 'aac':1, ...},
                                                                  'aac':{'aaa':2, 'aac':15, ...},
                                                                  ...}
                                       'species_X_to_species_Z': {'aaa':{'aaa':8, 'aac':2, ...},
                                                                  'aac':{'aaa':3, 'aac':10, ...},
                                                                  ...}
                                       ...}

Various values (IVRYWEL, EKQH, PAYRESDGM, GC12 ...) are stored in int/float variables, put in a list (one per species) and then also written in csv file.

Source code
===========

.. py:function:: buildDicts(list_codons, content, dict_genetic_code, dict_aa_classif)

   :param list_codons: all codons except stop-codons
   :type list_codons: list
   :param content: specifies the keys type 
   :type content: int (for countings) or list (for resampling)
   :param dict_genetic_code: Describe the genetic code (key : amino-acid, values : list of codons)
   :type dict_genetic_code: dict
   :param dict_aa_classif: Describe the amino-acids classification (key : amino-acid type, values : list of amino-acids)
   :type dict_aa_classif: dict
   :return: 6 dictionaries used for countings and frequencies storage on a species or resampling
   :rtype: dict

At each species or pair of species countings, a copy of the input dictionaries is done, and dictionaries for transitions are also built. 

.. py:function:: viable(seqs, pos, method)

   :param seqs: sequences, which must all have the same size
   :type seqs: list
   :param pos: positions, <= len(seqs) -3
   :type pos: int
   :param method: "all" or "any"
   :type method: string
   :return: if the given position is viable in all the sequence
   :rtype: bool

Compute if, among a set of sequences, either at least one of the codons at the specified position has not a "-", or not any codon has a "-"

.. py:function:: computeAllCountingsAndFreqs(seq, list_codons, init_dict_codons, init_dict_aa, init_dict_classif, dict_genetic_code, dict_aa_classif)

   :param seq: a dna/rna sequence (a,t,c,g)
   :type seq: string
   :param list_codons: all codons except stop-codons
   :type list_codons: list
   :param init_dict_codons: a copied output of `buildDicts()`
   :type init_dict_codons: dict
   :param init_dict_aa: a copied output of `buildDicts()`
   :type init_dict_aa: dict
   :param init_dict_classif: a copied output of `buildDicts()`
   :type init_dict_classif: dict
   :param dict_genetic_code: Describe the genetic code (key : amino-acid, values : list of codons)
   :type dict_genetic_code: dict
   :param dict_aa_classif: Describe the amino-acids classification (key : amino-acid type, values : list of amino-acids)
   :type dict_aa_classif: dict
   :returns: 6 variables : countings and frequencies of codons, amino-acid and amino-acid types
   :rtype: dict

Call all functions dedicated to the computation of occurences and frequencies of codons, amino-acids, amino-acids types

.. py:function:: computeVarious(seq, dict_aa_counts, dict_aa_types)

   :param seq: a dna/rna sequence (a,t,c,g)
   :type seq: string
   :param dict_aa_counts: the countings of amino-acids on the param `seq`
   :type dict_aa_counts: dict
   :param dict_aa_types: the countings of amino-acids types on the param `seq`
   :type dict_aa_types: dict
   :returns: GC3, GC12, IVYWREL, EKQH, PAYRESDGM, purineload, CvP values for `seq`
   :rtype: int and floats

Call al the functions for nucleic and amino-acids sequences description : *GC3, GC12, IVYWREL, EKQH, PAYRESDGM, purineload, CvP*

.. py:function:: computeAllBiases(seq1, seq2, dico_codons_transi, dico_aa_transi, dico_aatypes_transi, reversecode, reverseclassif)

   :param seq1: a dna/rna sequence (a,t,c,g)
   :type seq1: string
   :param seq2: a dna/rna sequence (a,t,c,g)
   :type seq2: string
   :param dico_codons_transi: a copied output of `buildDicts()`
   :type dico_codons_transi: dict of dicts
   :param dico_aa_transi: a copied output of `buildDicts()`
   :type dico_aa_transi: dict of dicts
   :param dico_aatypes_transi: a copied output of `buildDicts()`
   :type dico_aatypes_transi: dict of dicts
   :param reversecode: the genetic_code, but reversed (key : codon, values : amino-acid)
   :type reversecode: dict
   :param reverseclassif: the amino-acids classification, but reversed (key : amino-acid, values : amino-acid type)
   :type reverseclassif: dict
   :returns: countings and frequencies of codons, amino-acid and amino-acid types transitions from `seq1` to `seq2`
   :rtype: dict of dicts

Compute all biases (transisitions codon->codon, aa->-aa, aa_type->aa_type) between two sequences 

.. py:function:: sampling (dict_seq, nb_iter, len_sample, list_codons, genetic_code, aa_classif, reversecode, reverseclassif)

   :param dict_seq: contains the species name (keys) and sequences (values) used
   :type dict_seq: dict
   :param nb_iter: the number of iterations
   :type nb_iter: int
   :param len_sample: the lenght (in codons) of each resampled sequence
   :type len_sample: int
   :param list_codons: all codons except stop-codons
   :type list_codons: list
   :param genetic_code: Describe the genetic code (key : amino-acid, values : list of codons)
   :type genetic_code: dict
   :param aa_classif: Describe the amino-acids classification (key : amino-acid type, values : list of amino-acids)
   :type aa_classif: dict
   :param reversecode: the genetic_code, but reversed (key : codon, values : amino-acid)
   :type reversecode: dict
   :param reverseclassif: the amino-acids classification, but reversed (key : amino-acid, values : amino-acid type)
   :type reverseclassif: dict
   :returns: 6 variables
   :rtype: dict and dict of dicts

Resample randomly codons from sequences (sort of bootsrap)

.. py:function:: testPvalues(dict_counts, dict_resampling, nb_iter)

   :param dict_counts: contains codons frequencies of a sequence, an output of `computeAllCountingsAndFreqs()`
   :type dict_counts: dict
   :param dict_resampling:
   :type dict_resampling: dict
   :param nb_iter: the number of iterrations used for resampling
   :type nb_iter: int
   :return: pvalues for each item (codon, amino-acid, amino-acid type countings or transitions)
   :rtype: dict

Computes where the observed value is located in the expected counting distribution


Back to `main page <index.html>`_.