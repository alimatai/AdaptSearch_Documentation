********
Pairwise
********


Overview
********

This tool searches for different homologous genes (tblastx with RBH) from pairwise comparisons between a set of fasta files (one file per species). It returns lists of homologous genes pairs between each pair of species

User parameters
===============

 #. Fasta files (outputs of `Filter_Assemblies <Filter_Assemblies.html>`_) separated by commas
 #. The blast e-value


Code documentation
******************

Algorithm
=========

.. todo:: Algo !

Source code
===========

.. todo:: source code docs !

File: functions.py
------------------

.. py:function:: split_file(path_in, keyword)

   :param path_in:
   :type path_in: string
   :param keyword:
   :type keyword: string
   :return:
   :rtype: dict

.. py:function:: detect_Matches(query, MATCH, WORK_DIR, bash1)

   :param query:
   :type query: 
   :param MATCH:
   :type MATCH: int
   :param WORK_DIR: source directory for blast run
   :type WORK_DIR: string
   :param bash1:
   :type bash1: dict
   :return:
   :rtype: 

.. py:function:: get_information_on_matches(list_of_line)

   :param list_of_line:
   :type list_of_line: 
   :return: match informations (length, score, identity percent ...)
   :rtype: list

.. py:function:: get_pairs(fasta_file_path)

   :param fasta_file_path: the path to a file
   :type fasta_file_path: string
   :return: returns all couples of homologous genes, each couple being a four elements sub-list (header+sequence * 2)
   :rtype: list of lists

.. py:function:: extract_length(length_string)

   :param length_string:
   :type length_string: string
   :return:
   :rtype: int

.. py:function:: filter_redondancy(list_paireu, MIN_LENGTH)

   :param list_paireu: list of list of matched sequences (pairs)
   :type list_paireu: list
   :param MIN_LENGTH: mimimum sequence length
   :type MIN_LENGTH: int
   :return: a list of dicts
   :rtype: list

File : S10_compare_list_pairs_for_reciprocical_best_hits_test.py
----------------------------------------------------------------

.. py:function:: get_pairs(fasta_file)

   :param fasta_file: the path to a file
   :type fasta_file: string
   :return: returns all couples of homologous genes, each couple being a four elements sub-list (header+sequence * 2)
   :rtype: list of lists

.. py:function:: get_short_name(long_name):

   :param long_name: a sequence's header
   :type long_name: string
   :return: shortened `long_name`
   :rtype: string


Back to `main page <index.html>`_.