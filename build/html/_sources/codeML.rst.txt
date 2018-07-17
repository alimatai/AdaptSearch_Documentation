******
codeML
******


Overview
========

codeml is a part of the PAML package, which is a suite of programs for phylogenetic analyses of DNA or protein sequences using maximum likelihood (ML).

codeml carries out ML analysis of :

 #. protein-coding DNA sequences using codon substitution models (e.g., Goldman and Yang 1994)
 #. amino acid sequences under a number of amino acid substitution models.


User parameters
===============

Full readme at http://abacus.gene.ucl.ac.uk/software/pamlDOC.pdf

The most important parameters are the model choices, 'branch model' and 'Site model'. The two of them can be combined to run mixed models.

.. warning:: Incompatibilities between modesl are automatically excluded from the selection, but some can remain (we haven't tested everything yet)!

.. note:: Don't forget to set the 'Sequences format in the fasta file' advanced parameter according to the format of your alignement ! The AdaptSearch suite is configured to run with nucleic format by default, but it is still possible to perform analyses on proteic data (the tool CDS_Search can return proteic sequences, and ConcatPhyl can handle them)

References
==========

Publication : Yang, Z. (2007). PAML 4: Phylogenetic Analysis by Maximum Likelihood. In Molecular Biology and Evolution, 24 (8), pp. 1586–1591. [doi:10.1093/molbev/msm088]

Website : http://abacus.gene.ucl.ac.uk/software/paml.html

Back to `main page <index.html>`_.