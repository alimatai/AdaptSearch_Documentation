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

.. warning:: Incompatibilities between models are automatically excluded from the selection, but some can remain (we haven't tested everything yet)!

.. note:: Don't forget to set the 'Sequences format in the fasta file' advanced parameter according to the format of your alignement ! The AdaptSearch suite is configured to run with nucleic format by default, but it is still possible to perform analyses on proteic data (the tool CDS_Search can return proteic sequences, and ConcatPhyl can handle them)

.. todo:: Some models require to edit the tree file (to set different ratios on branches) ; for now, there is no automatic way to do that : the user has to download the treeFile from Galaxy, edit it, and reload it in Galaxy. It could be interesting to dive more into the ete3 Python module, which implements a tree class, to see if there is an efficient way to edit trees.

The codeml config file
----------------------

The program codeML reads all the model parameter from a pre-formatted configfile ('codeml.ctl'), which is written within Galaxy, based on the parameters specified by the user. This config file looks like this :

The config file looks like this :

.. code-block:: none

       seqfile = fastaAlignementFile * sequence data file name 
       outfile = outputDirName * main result file name 
      treefile = newickTreeFile * tree structure file name 
         noisy = 9  * 0,1,2,3,9: how much rubbish on the screen 
       verbose = 0  * 1: detailed output, 0: concise output 
       runmode = 0  * 0: user tree;  1: semi-automatic;  2: automatic 
                    * 3: StepwiseAddition; (4,5):PerturbationNNI; -2: pairwise 
       seqtype = 1  * 1:codons; 2:AAs; 3:codons-->AAs 
     CodonFreq = 1  * 0:1/61 each, 1:F1X4, 2:F3X4, 3:codon table 
 *       ndata = 10 
         clock = 0   * 0:no clock, 1:clock; 2:local clock; 3:TipDate 
        aaDist = 0  * 0:equal, +:geometric; -:linear, 1-6:G1974,Miyata,c,p,v,a 
                    * 7:AAClasses 
    aaRatefile = wag.dat * only used for aa seqs with model=empirical(_F) 
                   * dayhoff.dat, jones.dat, wag.dat, mtmam.dat, or your own 
         model = 0 
                    * models for codons: 
                        * 0:one, 1:b, 2:2 or more dN/dS ratios for branches 
                    * models for AAs or codon-translated AAs: 
                        * 0:poisson, 1:proportional,2:Empirical,3:Empirical+F 
                        * 6:FromCodon, 8:REVaa_0, 9:REVaa(nr=189) 
       NSsites = 0  * 0:one w;1:neutral;2:selection; 3:discrete;4:freqs; 
                    * 5:gamma;6:2gamma;7:beta;8:beta&w;9:beta&gamma; 
                    * 10:beta&gamma+1; 11:beta&normal>1; 12:0&2normal>1; 
                    * 13:3normal>0 
         icode = 0  * 0:universal code; 1:mammalian mt; 2-11:see below 
         Mgene = 0  * 0:rates, 1:separate;  
     fix_kappa = 0  * 1: kappa fixed, 0: kappa to be estimated 
         kappa = 2  * initial or fixed kappa 
     fix_omega = 0  * 1: omega or omega_1 fixed, 0: estimate  
         omega = 0.2 * initial or fixed omega, for codons or codon-based AAs
     fix_alpha = 1  * 0: estimate gamma shape parameter; 1: fix it at alpha 
         alpha = 0.0 * initial or fixed alpha, 0:infinity (constant rate) 
        Malpha = 0  * different alphas for genes 
         ncatG = 3  * # of categories in dG of NSsites models 
       fix_rho = 1  * 0: estimate rho; 1: fix it at rho 
           rho = 0. * initial or fixed rho,   0:no correlation 
         getSE = 0  * 0: don't want them, 1: want S.E.s of estimates 
  RateAncestor = 0  * (0,1,2): rates (alpha>0) or ancestral states (1 or 2)
    Small_Diff = 5e-07
     cleandata = 0  * remove sites with ambiguity data (1:yes, 0:no)?
   fix_blength = 0   * 0: ignore, -1: random, 1: initial, 2: fixed
        method = 0   * 0: simultaneous; 1: one branch at a time


References
==========

Publication : Yang, Z. (2007). PAML 4: Phylogenetic Analysis by Maximum Likelihood. In Molecular Biology and Evolution, 24 (8), pp. 1586–1591. [doi:10.1093/molbev/msm088]

Website and manual : http://abacus.gene.ucl.ac.uk/software/paml.html

Back to `main page <index.html>`_.