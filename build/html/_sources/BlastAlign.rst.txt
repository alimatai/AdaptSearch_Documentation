**********
BlastAlign
**********


Overview
========

BlastAlign takes a set of nucleic sequences in a file in fasta format and returns a multiple alignment (in Nexus and Phylip formats) using BLAST+. This Galaxy implementation works with dataset collections, which allows multiple parallels runs of BlastAlign at once on many files.

This tool has been chosen for it's good ability to manage indels.

.. note:: If the sequences cannot be aligned, the corresponding file in the output dataset collection will be empty, but not marked as failed. This behavior is the result of slight modifications of BlastAlign source code. The origin code caused a tool crash, which was not relevant outside Galaxy but had to be managed in a more Galaxy's dataset collections suitable way, leading to these empty files. The output collection has to be cleaned of these empty files with the tool 'Filter_failed' from the 'Colelction operations' panel.

.. note:: The 'Filter failed' tool is not supposed to filter green empty files ; it's a temporary modification on our Galaxy instance, and a new tool, 'Filter_empty' is supposed to be available soon.

User parameters
===============

 * Fasta file(s) : outputs of `POGs <POGs.html>`_ ; under Galaxy, it can be one or many files
 * Output format : choice between fasta (default for AdaptSearch), phylip or nexus (default for stand-alone BlastAlign)
 * Blast advanced options
 
  - Proportion of gaps allowed in any one sequence in the final alignement : (default 95, i.e. only removing sequences with extremely short matches (-m))
  - Reference sequence : enter the header of a sequence which will be used as reference for blast (default : deactivated)
  - Sequences to exlude : enter sequences' header to exlude them from the analysis (default : deactivated)
  - Retain original names in output files : keeps trace of the inputs headers' format (default : activated)


References
==========

Publication : Belshaw R, Katzourakis A (2005) BlastAlign: a program that uses blast to align problematic nucleotide sequences. Bioinformatics 21:122-3

Source code : http://evolve.zoo.ox.ac.uk/Evolve/Blastalign.html


AdaptSearch supplemental code
=============================

**phylip to fasta format converter :** converts BlastAlign standard phylip output in fasta. A proper exit is implemented if the specified phylip file does not exist.

.. py:function:: format(File_IN)

   :param File_IN: a phylip file
   :type File_IN: file
   :returns: the content of converted phylip file (String) and the number of species within (int)
   :rtype: String, int


Back to `main page <index.html>`_.