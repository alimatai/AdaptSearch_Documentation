**********
BlastAlign
**********


Overview
********

BlastAlign takes a set of nucleic sequences in a file in fasta format and returns a multiple alignment (in Nexus and Phylip formats) using BLAST+. This Galaxy implementation works with dataset collections, which allows multiple parallels runs of BlastAlign at once on many files.

This tool has been chosen for it's good ability to manage indels.

User parameters
===============

 #. Fasta file(s) : outputs of `POGs <POGs.html>`_ ; under Galaxy, it can be one or many files
 #. Output format : choice between fasta (default for AdaptSearch), phylip or nexus


References
==========

Publication : Belshaw R, Katzourakis A (2005) BlastAlign: a program that uses blast to align problematic nucleotide sequences. Bioinformatics 21:122-3

AdaptSearch supplemental code
*****************************

**phylip to fasta format converter :** converts BlastAlign standard phylip output in fasta. A proper exit is implemented if the specified phylip file does not exist.

.. py:function:: format(File_IN)

   :param File_IN: a phylip file
   :type File_IN: file

Function to convert a phylip file to a fasta file.


Back to `main page <index.html>`_.