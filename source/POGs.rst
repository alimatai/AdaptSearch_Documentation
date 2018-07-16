**** 
POGs 
****


Overview
========

POGs (Putative Orthologs Groups) parses outputs of `Pairwise <Pairwise.html>`_ to build groups of putative orthologous genes.

User parameters
===============

The parameter's name are the ones used in the Galaxy's interface.
 #. Input files from Pairwise DNA : all the `Pairwise_DNA <Pairwise.html>`_ outputs, separated by commas
 #. (integer) Drop orthogroups with less than n species : smaller orthogroups will not be returned. It is recommended to set this number to the total number of studied species, to avoid missing data afterwards
 #. (-v) Verbose : display a supplementary table with details before the paralogous sequence removal
 #. (-p) Paralogs : if activated, returns the orthogroups before paralogs sequences removal


Code documentation
==================

Algorithm
---------

Pairwise outputs are gathered into a list of lists of couples of homologous sequences obtained by blast, for instance :

.. code-block:: python

    [[sequence_1, sequence_2],[sequence_3, sequence_4],[sequence_1, sequence_5],[sequence_3, sequence_6], ...]

The main algorithm gathers together groups sharing sequences :

.. code-block:: python

    group 1 : [sequence_1, sequence_2, sequence_5]
    group 2 : [sequence_3, sequence_4, sequence_6]
    ...

There are two ways to deal with sequences from the same species (paralogs) within a group:
 #. Only the first sequence recorded is kept. These outputs are always returned byt the program
 #. All sequences are kept (this gives supplemntal outputs)

Each orthogroup is then written in a fasta file. The log displays a dataframe indicating the numbers of orthogroups and the numbers of species per orthogroups after the removal of paralogous sequences.

.. code-block:: none

    list_orthogroups = []

    FOR genes_pair IN all_genes_pairs DO

        IF genes_pair NOT tagged THEN
            orthogroup = genes_pair

            FOR genes_pair_2 IN all_genes_pairs[starting from genes_pairs+1] DO
                IF intersection(orthogroup, genes_pair_2) AND genes_pair_2 NOT tagged THEN
                    UPDATE orthogroup WITH genes_pair_2

            IF len(list_orthogroup) > 0 THEN
                presence = False
                FOR final_group in list_orthogroup DO
                    IF intersection(orthogroup, final_group) THEN
                        UPDATE final_group WITH orthogroup
                        presence = True
                IF NOT presence
                    list_orthogroups APPEND orthogroup
            ELSE
                list_orthogroup APPEND orthogroup

    RETURN list_orthogroups



Source code
-----------

.. py:class:: Locus(header, sequence, tagged)

   :param header: a nucleic/proteic sequence's header
   :type header: string
   :param sequence: a nucleic/proteic sequence
   :type sequence: string
   :param tagged: used in orthogroup's building functions
   :type tagged: bool

Describe a sequence with its header and its nucleic/proteic sequence. The *tag* parameter is used in the function **makeOrthogroups** (sub-routine : **checkIfTagged** and **tagGroup**) to avoid duplicated loci and splitted groups.

.. py:function:: getListPairwiseAll(listPairwiseFiles)

   :param listpairwiseFiles:  the pairwise files (tool's input files)
   :type listpairwiseFiles: list
   :return: all the homologous genes pairs
   :rtype: list of list of Locus objects

Proceeds to create a list of lists of Locus objects. Calls the sub-routine **getPairwiseCouple** on each element of *listPairwiseFiles*

.. py:function:: makeOrthogroups(list_pairwises_allsp, minspec, nb_rbh, verbose, paralogs)

   :param list_pairwises_allsp: all the homologous genes pairs
   :type list_pairwises_allsp: list of list of Locus objects
   :param minspec: minimal number of species per group (set by the user)
   :type minspec: int
   :param nb_rbh: number of pairwise comparisons (automatically pre-computed)
   :type nb_rbh: int
   :param verbose: set by -v option ; activate the display of an intermediate summary table (orthogroups before paralogs removal)
   :type verbose: bool
   :param paralogs: set by -p option ; activate the writing of intermediate output files (orthogroups before paralogs removal)
   :type paralogs: bool
   :return: the number of orthogroups
   :rtype: int

This is the main function, which creates the orthogroups by parsing the list returned by *getListPairwiseAll* and writes output files

makeOrthogroups() sub-routines
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. py:function:: checkIfTagged(pair)

   :param pair: a pair of sequences
   :type pair: list of two Locus objects
   :return: wether the Locus objects have been already checked while makeOrthogroups() looping step.
   :rtype: bool

This function is used while making orthogroups. When a Locus pairs is added to an orthogroup while looping on the list of homologous sequences pairs, both Locus objects are tagged (*True*) to avoid them to be associated with following pairs, leading to unwanted splitted orthogroups.

.. py:function:: writeOutputFile(orthogroup, number, naming)

   :param orthogroup: contains all sequences of an orthogroup
   :type orthogroup: set
   :param number: used to set a unique filename
   :type number: int
   :param naming: the file name varies according to the value of this parameter (depends on the *paralogs* option)
   :type naming: bool

Used to write an orthogroup in an output fasta file.

.. py:function:: filterParalogs(list_orthogroups, minspec)

   :param list_orthogroups: computed in makeOrthogroups()
   :type list_orthogroups: list of sets of Locus objects
   :param minspec: minimal number of species per group (set by the user)
   :type minspec: int
   :return: the orthogroups with only one sequence per species
   :rtype: list of list of Locus objects

Remove sequences from the same species and keeps only the first sequence (*Locus* object) encountered

.. py:function:: countings(listOrthogroups, nb_rbh)

   :param listOrthogroups: computed in makeOrthogroups()
   :type listOrthogroups: list of sets of Locus objects
   :param nb_rbh: the number of pairwise comparisons (e.g number of input files)
   :type nb_rbh: int
   :return: a summary table containing the number of orthogroups and the number of species per orthogroup.
   :rtype: numpy 2D array

.. py:function:: asFrame(matrix)

   :param matrix: the output of countings()
   :type matrix: numpy 2D array
   :return: the matrix converted in a pandas dataFrame


Back to `main page <index.html>`_.
