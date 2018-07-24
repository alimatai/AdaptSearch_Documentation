**********
ConcatPhyl
**********


Overview
========

This tool takes files containing fasta sequences (from CDS_Search outputs in the AdaptSearch suite, in nucleic or proteic format), concatenate all the sequences in a super-alignment, and run RAxML to build a phylogeny. 

.. note:: By default, AdaptSearch will run with nucleic format, but you can choose to use proteic format from CDS_Search to run ConcatPhyl and CodeML.


User parameters
===============

Inputs
------

 * Files from Filter Assemblies : a set of fasta files (one file per species), e.g. the outputs of the first tool of the AdaptSearch suite. Used to retrieve all the species names.
 * Alignment files without indels : a set of fasta files with aligned sequences (with the same species than into the previous parameter), e.g the outputs of the CDS_Search tool of the AdaptSearch suite. 
 * Format choice : nucleic or proteic (according to the CDS_Search output you choose).

RAxML parameters
----------------

 * Substitution model (-m) : odel of Binary (Morphological), Nucleotide, Multi-state, or Amino-Acid substitution. Default : GTRGAMMA (nucleic), PROTCAT (proteic)
 * Matrix : AA substitution model (only when using proteic inputs). Default : DAYHOFF
 * random seed : Specifies a random number seed for the parsimony inferences. For all options/algorithms in RAxML that require some sort of randomization, this option must be specified. Make sure to pass different random number seeds to RAxML and not only 12345
 * Number of runs (-N) : Specifies the number of alternative runs. Default : 100
 * Use bootstopping criteria for number of runs : if selected, overxwrites the number of runs to use bootstopping criteria
 * Algorithm to execute (-f) : allows to choose what kind of algorithme RAxML shall execute. Default : Rapid bootsrap and best ML tree search (-f a)
 * Multiple model assignement to alignment partitions (-q) : an optional parameter. Permits to specify the file name which contains the assignment of models to alignment partitions for multiple models of substitution. For the syntax of this file please consult the manual.
    This option allows you to specify the regions of your alignment for which an individual model of nucleotide substitution should be estimated. This will typically be useful to infer trees for long multi-gene alignments.
 * Rapid bootstrapping random seed (-x) : Specify an integer number (random seed) and turn on rapid bootstrapping. In addition to the best tree search. By default, this option is choosen.

Outputs
-------

The output files vary according to the

 * Phylogeny : the general output. It gives the information about the concatenation (statistics) and the RAxML run.

Concatenation files
^^^^^^^^^^^^^^^^^^^

The file name varies according to format parameters :

 * Phylogeny_concatenation_fasta_aa : sequences concatenated in fasta format when proteic format is chosen
 * Phylogeny_concatenation_phylip_aa : sequences concatenated in phylip format when proteic format is chosen
 * Phylogeny_concatenation_nexus_aa : sequences concatenated in nexus format when proteic format is chosen
 * Phylogeny_concatenation_fasta_nuc : sequences concatenated in fasta format when nucleic format is chosen
 * Phylogeny_concatenation_phylip_nuc : sequences concatenated in phylip format when nucleic format is chosen. **This is the output used for the RAxML run**
 * Phylogeny_concatenation_nexus_nuc : sequences concatenated in nexus format when nucleic format is chosen

RAxML outputs
^^^^^^^^^^^^^

 * Phylogeny_RAxML_BestTree** : the Best Tree found.
 * Phylogeny_RAxML_BiPartitionBranchLabel : the Best Tree found with supported values as branch labels.
 * Phylogeny_RAxML_BiPartition : the Best Tree found with supported values.
 * Phylogeny_RAxML_BootStrap : all the boostrapped trees. The number of boostraped trees depending of the option -N (number of run).

Code documentation
==================

Algorithm
---------

See publication : 

Stamatakis A. RAxML version 8: a tool for phylogenetic analysis and post-analysis of large phylogenies. Bioinformatics. 2014;30(9):1312-1313. doi:10.1093/bioinformatics/btu033.

Source code
-----------

The python script, 'S01_concatenate.py' paste all the aligned orthogroups in a super-alignment, in fasta, phylip or nexus format

.. py:function:: dico(F2)

    :param F2: A fasta file (aligned orthogroup) name
    :type F2: String
    :return dicoco: a dictionary of sequences in the orthogroup, headers being the keys and sequences being the values
    :rtype: dict

This function is called on each input orthogroup fasta file in the function concatenate()

.. py:function:: concatenate(L_IN, SPECIES_ID_LIST)

    :param L_IN: A list of aligned orthogroups fasta files
    :type L_IN: list
    :param SPECIES_ID_LIST: The list of all studied species (abbreviated names)
    :param SPECIES_ID_LIST: list
    :return: bash_concat (dict) - the dictionary of the super-alignment
    :return: ln_concat (int) - the length of the super-alignment
    :return: nb_locus (int) - the number of concatenated orthogroups 
    :return: list_genes_position (list of list) - the list of positions (with names of implied files) where each concatenantion is made

    This function builds a dictionary with all the species as key. The values are nucleic or proteic sequences, pasted alltogether along with iterations over the files in L_IN. '-' are inserted in a concatened sequence when a species is missing in a group.

.. py:function:: get_codon_position(seq_inORF)

    :param seq_inORF: a nucleic sequence
    :type seq_inORF: String
    :return: 4 Strings, storing the letter at a given position of each codon - the first one contains the first letter of each codons, the second contains the second letter of each codon, the third contains the first and second letters of each codons, the fourth contains the 3rd letter of each codon

.. code-block:: python

    seq='catgctagctagagaact'
    a,b,c,d = get_codon_position(seq)
    print a,b,c,d
    
    > cgataa acgagc cagcagtaagac ttcgat

References
==========

RAxML publication : Stamatakis A. RAxML version 8: a tool for phylogenetic analysis and post-analysis of large phylogenies. Bioinformatics. 2014;30(9):1312-1313. doi:10.1093/bioinformatics/btu033.

RAxML Manual : https://sco.h-its.org/exelixis/resource/download/NewManual.pdf


Back to `main page <index.html>`_.