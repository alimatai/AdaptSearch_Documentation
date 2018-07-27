**************************
Analysis of codeML results
**************************


Overview
========

These scripts call functions from the python module ETE Toolkit and have been developepd to work in two ways :

Handling of codeML runs on a super-alignment from AdaptSearch's Galaxy instance
-------------------------------------------------------------------------------

This way of working is for analysis on the super-alignement obtained from the tool Concatphyl, on which the user can have ran several model of codeML. The python script receives all the results files and directory and proceeds to run model comparisons by Maximum Likelihood analysis. For now, all the results have to be downloaded from Galaxy, which can be really boring ; in a close future, we could add the script to the Galaxy suite

Running an evolutionary model on many differents alignments at once :
---------------------------------------------------------------------

This way have been developped in the case the user wants to test evolutionary hypothesis on the aligned orthogroups before the concatenation into a super-alignement by the tool Concatphyl. The script receives all the alignement fasta files and run the same model on each of them. Results are stored into directories, one per input file. Ideally, it should be possible to run several models and to perform ML comparison on each model (per file)

- For now, The tree file is the tree computed by the super-alignement !
- **The orthogroups must be complete (all studied species within) !**

.. note:: This script can also work to run models on the super-alignment produced by Concatphyl. Maybe we should consider to implement it under Galaxy. Indeed, the ete3 Python module incorporates the whole PAML suite, with pre-built models, access to all parameters, and various explanations


Usage
=====

.. todo:: Some models require to edit the tree file (to set different ratios on branches) ; for now, there is no automatic way to do that : the user has to download the treeFile from Galaxy, edit it, and reload it in Galaxy. It could be interesting to dive more into the ete3 Python module, which implements a tree class, to see if there is an efficient way to edit trees.

.. todo:: There is currently a unfinished Galaxy's wrapper.

Script for exploitation of codeML results : load_precomputed_model.py
---------------------------------------------------------------------

The arguments are the RAxML best tree and the super-alignment computed by Concatphyl, and a text file with the list of models computed by codeML (either under Galaxy or without Galaxy).

.. code-block:: none

    # command line :
    ./load_precomputed_model.py /path/to/phylogenetic/tree/used/in/codeML /path/to/super/alignment/used/in/codeML /path/to/file/with/list/of/models
    # Example with test-data files :
    ./scripts/load_precomputed_model.py test-data/concatenation_4_species.fasta test-data/tree_4_species.nwk test-data/pre_computed_models/models_list 


    # models list file should be like that (one model per line) :
    M0 /path/to/M0/Model/out
    M1 /path/to/M1/Model/out
    M2 /path/to/M2/Model/out
    fb /path/to/fb/Model/out
    
    etc. The 'out' file in the path is the main codeml output, which can have another name (unde Galaxy, it is named *run_codeml*)

All the models are charged in memory, and then all possible ML ratios tests are performed to determine the best model. The Summary table is printed on screen/log file and written into a csv file.

.. note:: ML tests between two models are possible if :
    #. The alternative model has more parameters than the null model
    #. The difference log_likelihood(null_model) - log_likelihood(alternative model) must be under 0 (log_likelihoods are always negative numbers, so Log_likelihood(null_model) < log_likelihood(alternative model))
    #. For implementation purpose, **the models names must be different** : if you ran two models M0 with different parameters, name them something like 'M0_a' and 'M0_b'. This is a point to improve in the future.


Script for running codeML models on one or many alignments : load_precomputed_model.py
---------------------------------------------------------------------------------------

The script takes one fasta alignment and its associated tree and runs all the models specified by the user

.. code-block:: none

    # command line :
    ./compCodeML_ete_oneFile.py /path/to/phylogenetic/tree/used/in/codeML /path/to/super/alignment/used/in/codeML list,of,models,to,run,comma,separated

 - The tree computed on the super-alignment can be used, but you can also run RAxML on each alignement to have one tree per alignement
 - It is planned to run this script in parallel on many input files.
 - The models names must be as written in ete3 (see table below).

ETE available models and comparisons:
=====================================

Models implemented in ete3 module
---------------------------------

.. code-block:: python

    # Available models

    # Name   | Description                 | Model kind
    # ------------------------------------------------
    # M1     | relaxation                  | site
    # M10    | beta and gamma + 1          | site
    # M11    | beta and normal > 1         | site
    # M12    | 0 and 2 normal > 2          | site
    # M13    | 3 normal > 0                | site
    # M2     | positive-selection          | site
    # M3     | discrete                    | site
    # M4     | frequencies                 | site
    # M5     | gamma                       | site
    # M6     | 2 gamma                     | site
    # M7     | relaxation                  | site
    # M8     | positive-selection          | site
    # M8a    | relaxation                  | site
    # M9     | beta and gamma              | site
    # SLR    | positive/negative selection | site
    # M0     | negative-selection          | null
    # fb_anc | free-ratios                 | branch_ancestor
    # bsA    | positive-selection          | branch-site
    # bsA1   | relaxation                  | branch-site
    # bsB    | positive-selection          | branch-site
    # bsC    | different-ratios            | branch-site
    # bsD    | different-ratios            | branch-site
    # b_free | positive-selection          | branch
    # b_neut | relaxation                  | branch
    # fb     | free-ratios                 | branch
    # XX     | User defined                | Unknown

Usual comparisons
-----------------

.. code-block:: python

    # Usual comparisons are :

    # ============ ======= ===========================================
    #  Alternative  Null    Test
    # ============ ======= ===========================================
    #   M2          M1      PS on sites (M2 prone to miss some sites)
    #                       (Yang 2000).
    #   M3          M0      test of variability among sites
    #   M8          M7      PS on sites
    #                       (Yang 2000)
    #   M8          M8a     RX on sites?? think so....
    #   bsA         bsA1    PS on sites on specific branch
    #                       (Zhang 2005)
    #   bsA         M1      RX on sites on specific branch
    #                       (Zhang 2005)
    #   bsC         M1      different omegas on clades branches sites
    #                       ref: Yang Nielsen 2002
    #   bsD         M3      different omegas on clades branches sites
    #                       (Yang Nielsen 2002, Bielawski 2004)
    #   b_free      b_neut  foreground branch not neutral (w != 1)
    #                        - RX if P<0.05 (means that w on frg=1)
    #                        - PS if P>0.05 and wfrg>1
    #                        - CN if P>0.05 and wfrg>1
    #                        (Yang Nielsen 2002)
    #   b_free      M0      different ratio on branches
    #                       (Yang Nielsen 2002)
    # ============ ======= ===========================================
    # **Note that M1 and M2 models are making reference to the new versions
    # of these models, with continuous omega rates (namely M1a and M2a in the
    # PAML user guide).**

    # **Alternative must have a greater number of parameters than Null**

.. note:: The tables above are also written in the Python scripts.

Code documentation
==================


Functions for the detection of positive selection : functions_positive_selection.py
-----------------------------------------------------------------------------------

.. warning:: These functions are not finished ! (implementation have been stopped for a while due to versions and dependancies conflicts).

.. py:function:: details_on_sites(model, tree, models_types, verbose)

   :param model: must be a site-model
   :type model: ete3 model object   
   :param tree: the tree used for running models
   :type tree: ete3 tree object
   :param models_types: the list of evolutionary models, sorted by type
   :type models_list: dict
   :param verbose: activate the display of conserved and neutral sites in the result
   :type verbose: bool

This function is for printing the location of the positively selected sites. Under model M1, there are no such sites (only w < 1 and w = 1)

.. py:function:: frame_site(model, tree, models_types, verbose)

   :param model: must be a site-model
   :type model: ete3 model object   
   :param tree: the tree used for running models
   :type tree: ete3 tree object
   :param models_types: the list of evolutionary models, sorted by type
   :type models_list: dict
   :param verbose: activate the display of conserved and neutral sites in the result
   :type verbose: bool

   This function works in the same way than details_on_sites() but writes the results in a csv table.

.. py:function:: details_on_branches(pval, alt_model, null_model, tree)

   :param model: must be a site-model
   :type model: ete3 model object   
   :param tree: the tree used for running models
   :type tree: ete3 tree object
   :param models_types: the list of evolutionary models, sorted by type
   :type models_list: dict
   :param verbose: activate the display of conserved and neutral sites in the result
   :type verbose: bool

This function is for printing positively selected branches on screen

.. py:function:: models_types()

   :return m_types: the list of models classified by their type (branch/site/branch-site/branch-ancestor/null)
   :rtype: dict


Conda environment
=================

I used a conda environment (with Python 2.7) to run these scripts :

.. code-block:: none

   # (1) The suite itself, for running in commandline in a terminal
   conda install -c etetoolkit ete3 ete_toolchain

   # (2) The Python module
   conda install -c etetoolkit ete3 

   # Dependancies will be automaticcaly installed with (1) and (2).

   # Pandas and numpy
   conda install -c anaconda numpy 
   conda install -c anaconda pandas

.. warning:: I had many versions and dependancies conflicts and crashes ! I suspect a conflict between Python versions (2.7/3.4) / PyQt versions (4/5) and ete3 versions. This needs to be investigated.

Reference
=========

ETE 3: Reconstruction, analysis and visualization of phylogenomic data. Jaime Huerta-Cepas, Francois Serra and Peer Bork. Mol Biol Evol 2016; doi: 10.1093/molbev/msw046

Website : http://etetoolkit.org

Back to `main page <index.html>`_.
