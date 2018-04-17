*****************
Filter_Assemblies
*****************


Overview
********

This tool reformats Velvet Oases or Trinity assemblies for the AdaptSearch galaxy suite and selects only one variant per gene according to its length and quality check (using cap3).

User parameters
===============

 #. A list of transcriptoms in fasta format, separated by commas.
 #. (integer > 65%) Overlap percent identity cutoff : minimum percent identity of an overlap
 #. (integer > 15) Overlap length cutoff : minimum length of an overlap (in base pairs)
 #. (integer) Minimum sequence length


Code documentation
******************

Algorithm
=========

.. code-block:: none

    # Main algorithm
    
    FOR each input file DO
        nameFormatting() on the file
            RUN the scripts S02a or S02b + S03 according to the used assembler
        CALL S04
        CALL cap3 and fasta_formatter
        CALL S05

.. todo:: The scripts creates various sub-directories and temp files, making it difficult to follow : TO IMPROVE.


Source code
===========

File: S01_script_to_choose.py
-----------------------------

.. py:function:: nameFormatting(name, script_path, prefix)
   
   :param name: the name of an input file
   :type name: string
   :param script_path: UNUSED - TO REMOVE FROM SOURCE CODE
   :type script_path: string
   :param prefix: A spcies' name two letter abbreviation
   :type prefix: string
   :return: a formatted name
   :rtype: string

This function is called on each input file, renames the file name and launches the scripts *S02b_format_fasta_name_trinity.py* and *S03_choose_one_variants_per_locus_trinity.py*

File: S02a_remove_redundancy_from_velvet_oases.py
-------------------------------------------------

.. py:function:: dico_filtering_redundancy(path_in)

  :param path_in: path to a asta file
  :type path_in: string
  :return: a dictionary headers/sequences from the fasta file, with simplified headers
  :rtype: dict

Filters redundancy (keeps only one variant epr locus) within velvet_oases assemblies

File: S02b_format_fasta_name_trinity.py
---------------------------------------

.. py:function:: dico_format_fasta_name(path_in, SUFFIX)

   :param path_in: path to a fasta file
   :type path_in: string
   :param SUFFIX: species' name abbreviation
   :type SUFFIX: string
   :return: a dictionary headers/sequences from the fasta file, with simplified headers
   :rtype: dict

Filters redundancy (keeps only one variant epr locus) within Trinity assemblies

File: S03_choose_one_variants_per_locus_trinity.py
--------------------------------------------------

.. py:function:: dico_filtering_redundancy(path_in)

  :param path_in: the path to a fasta file
  :type path_in: string
  :return: a dictionary header - sequence from `path_in` with only one variant per locus.
  :rtype: dict

File: S04_find_orf.py
---------------------

..py: function:: find_orf(entry)

   :param entry: a dna/rna sequence
   :type entry: string
   :return: start and stop positions of the longest ORF in `entry`
   :rtype: list

.. py:function:: reverse_seq(entry)

   :param entry: a dna/rna sequence (A,T,C,G,N)
   :type entry: string
   :return: the reverse complement of `entry`
   :rtype: string


Back to `main page <index.html>`_.