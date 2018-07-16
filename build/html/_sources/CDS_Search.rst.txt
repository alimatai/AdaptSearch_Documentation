**********
CDS_Search
**********


Overview
========

This tool takes files containing nucleic aligned sequences and search the ORF and the CDS.

User parameters
===============

For computing :
 #. (yes/no) Methionine : if 'yes' is checked (default), the Methionine will be considered in the search of CDS 
 #. (integer) Minimal number of species in each locus. It is recommended to set this number to the total number of studied species, to avoid missing data afterwards
 #. (integer) Minimal length of the CDS (in amino-acids)
 #. (integer) Minimal length of the subsequence (in amino-acids) between two series of indels
 #. (integer) Minimal length of the CDS, (in nucleotides) without indels

For outputs selection - All available in both nucleic and proteic format
 #. Activate outputs for files with the best ORF
 #. Activate outputs for files with CDS
 #. Activate outputs for files with CDS without indels (most important)


Code documentation
==================

Algorithm
---------

.. todo:: Algo !

Part. 1:
^^^^^^^^

Predict potential ORF on the basis of 2 criteria + 1 optional criteria :

 #. Longest part of the alignment of sequence without codon stop "*", tested in the 3 potential ORF
 #. This longest part should be > 150nc or 50aa
 #. [OPTIONNAL] A codon start "M" should be present in this longuest part, before the last 50 aa
    
    #. The output directory "05_CDS_aa" & "05_CDS_nuc" does not include this criteria
    #. The output directory "06_CDS_with_M_aa" & "06_CDS_with_M_nuc" includes this criteria

Part. 2:
^^^^^^^^

Find and remove indels

Source code
-----------

.. todo:: source code docs !

File : S01_find_orf_on_multiple_alignment.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. py:function:: code_universel(F1)

   :param F1: a txt file with the genetic code (each line : a codon and its amino-acid)
   :type F1: file
   :return: the genetic code (key : codon, value : amino-acid)
   :rtype: dict

Stop codons have the value '*'.

.. py:function :: find_good_ORF_criteria_3(bash_aligned_nc_seq, bash_codeUniversel)

   :param bash_aligned_nc_seq:
   :type bash_aligned_nc_seq: dict
   :param bash_codeUniversel:
   :type bash_codeUniversel: dict
   :returns: 6 dictionaries
   :rtype: dict

This is the most important fonction, which is run on each input file. The dictionaries return:
 #. All the sequences with best  (open reading frame)
 #. All the sequences with CDS (coding sequence)
 #. All the sequences with CDS without indels
 
In nucleic or proteic format

find_good_ORF_criteria_3() subroutines
""""""""""""""""""""""""""""""""""""""

.. py:function:: multiple3(seq)

   :param seq: a rna/dna sequence
   :type seq: string
   :return: an updated sequence and the length(seq)%3 modulo
   :rtype: tuple

Tests if a sequence is multiple of 3, which measn only full codons. If not, the end of the sequence is cut by one or two bases.

.. py:function:: simply_get_ORF(seq_dna, bash_codeUniversel)

   :param seq_dna: a dna/rna sequence (a,t,c,g)
   :type seq_dna: string
   :param bash_codeUniversel:
   :type bash_codeUniversel: dict
   :return: an amino-acid sequence translated from seq_dna (open-reading frame only)
   :rtype: string

.. py:function:: allindices(string, sub)

  :param string: a dna/rna sequence
  :type string: string
  :param sub: a smaller dna/rna sequence
  :type sub: string
  :return: the list of occurrences of the `sub`
  :rtype: list

Detects all indices corresponding to all occurrences of a substring in a string. This can be replaced by :

.. code-block:: python

   import re
   [motif.start() for motif in re.finditer(sub, string)]

.. py:function:: detect_Methionine(seq_aa, Ortho)

   :param seq_aa: an amino-acid sequence
   :type seq_aa: string
   :param Ortho: value indicating wether the first methionine found is not in the last 55 amino-acids.
   :type Ortho: int
   :return: an updated value of `Ortho`
   :rtype: int

Detect if methionin in the aa sequence. Calls `allindices()`

.. py:function:: ReverseComplement2(seq)

   :param seq: a dna/rna sequence (a,t,c,g)
   :type seq: string
   :return: The reverse complement of `seq`
   :rtype: string

File : S02_remove_too_short_bit_or_whole_sequence.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. py:function:: detect_short_indel(seq, MAX_LENGTH_SMALL_INDEL)

   :param seq: a dna/rna sequence (a,t,c,g)
   :type seq: string
   :param MAX_LENGTH_SMALL_INDEL:
   :type MAX_LENGTH_SMALL_INDEL: int
   :return: The list of consecutive gap positions
   :rtype: list of lists

.. code-block:: python

   detect_short_indel("agcga-ag----agact-aca--ga-----aaatg-aca---aaaa", 2)
   # [[5], [17], [21, 22], [35]]

   detect_short_indel("agcga-ag----agact-aca--ga-----aaatg-aca---aaaa", 7)
   # [[5], [8, 9, 10, 11], [17], [21, 22], [25, 26, 27, 28, 29], [35], [39, 40, 41]]

File : S03_remove_sties_with_not_enought_species_represented.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. py:function:: remove_position_with_too_much_missing_data(bash_aa, bash_nuc, MIN_SPECIES_NB)

   :param bash_aa: a hashtable sequence_name - proteic sequence
   :type bash_aa: dict
   :param bash_nuc: a hashtable sequence_name - nucleic sequence
   :type bash_nuc: dict
   :param MIN_SPECIES_NB:
   :type MIN_SPECIES_NB: int
   :returns: two dictionaries without sequences containing indels in `bash_aa` and `bash_nuc`
   :rtype: dict

`bash_aa` and `bash_nuc` are obtained at each input file iteration with the function `dico(fileIN)` from `dico.py` file.

Back to `main page <index.html>`_.
