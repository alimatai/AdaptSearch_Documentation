*****************************************************
User Guide for the use of AdaptSearch in command line
*****************************************************


.. topic:: Alternative to Galaxy

   Due to perfs issue on the Galaxy instance, a commandline usecase of AdaptSearch has been set up. Each tool can be launched with a qsub on Roscoff Marine Station cluster. This usecase is semi-automatic : each qsub has to be launched by the user.
   
=============
Usage example
=============

.. Warning:: Make sure to respect the items below :

 * The input start files names (transcriptoms) must **start with the species abbreviated name**, for example, if the species is *Alvinella pompejana* : Ap_transcriptom.fasta
 * The pipeline is set to work with the directories architecture defined in **/projet/umr7144/abice/amataigne/AdaptSearch_commandLine** : Do not modify any script name or location (same warning for directories), except if you copy the whole proejct elswhere. Maybe you'll have to ask for access to support.abims@sb-roscoff.fr).
 
.. note:: The pipeline suite is semi-automatic : each tool have to be run by the user (a given tool is not automatically launched by the previous one).

.. note:: Almost every tool can be launched without editing the default parameters, except MutCount, where the user has to edit the command lines to set groups of species for counts, resampling, and ecological categories.

-------------
Run the tools
-------------

First, go to the directory specified above ::

   ssh nz
   cd /projet/umr7144/abice/vmataigne/AdaptSearch_commandLine
   
Then, copy your input transcritoms in the directory::

   cp /path/to/your/files/*.fasta input_start_files/
   
Specify your email adress in each .sh file (edit **#$ -M alice.mataigne@sb-roscoff.fr**)
If you think that a tool will run for more than 12h, edit the line **#$ -q short.q** to **#$ -q long.q**
   
Now the pipeline can be launch. You'll have to launch each tool manually once the previous tool is finished::

   qsub Step_01_filter_assemblies.sh
   qsub Step_02_pairwise.sh
   qsub Step_03_pogs.sh
   qsub Step_04_blastalign.sh
   qsub Step_05_cds_search.sh
   qsub Step_06_concat_phyl.sh
   qsub Step_07_mutcount.sh
   qsub Step_08_codeml.sh 'directory_result_name'
   
The input and outputs files are automatically handled for each tool but :
   
.. Warning:: if you run a tool a second time (for instance with different parameters), the previous results directories must be renamed or moved in another directory ! If they are not, the tool will not be launched, or the previous results will be erased.

After each tool run, various temporary and intermediate files are stored in the directory  **archived_temp_files/**. Already stored results might also be erased if you run the same tool twice (or, the new temp files won't be moved and will stay in the main directory).

--------------------------------
How to modify default parameters
--------------------------------

You will have to edit directly the python command(s) line(s) in each shell (.sh) script to change default parameters. This is a quite unconvenient way to proceed so below is explained in details wich command line argument corresponds to which Galaxy's parameter

.. note:: These explains only the correspondances between commandline arguments and Galaxy's parameters. For more details about each parameter, see the tool's main help page.

~~~~~~~~~~~~~~~~~
Filter Assemblies
~~~~~~~~~~~~~~~~~

Command line to edit::

   python S01_script_to_choose.py $inputs 100 100 60

* $inputs : do not modify ; it is a shell variable storing inputs files names
* The 2d argument (default : 100) is the minimum sequence length
* The 3d argument (default : 100, minimum : 65) is the minimum percent identity of an overlap
* The 4d argument (default : 60, minimum : 15) is minimum length of an overlap (in base pairs)
   
~~~~~~~~
Pairwise
~~~~~~~~

Command line to edit::   
   
   python S01_run_first_blast.py $inputs 1e-5 diamond

* $inputs : do not modify ; it is a shell variable storing inputs files names
* The 2d argument is the e-value (default : 1e-5)
* The 3d argument is the alignment tool to use : use 'tblastx' or 'diamond'

~~~~
POGs
~~~~

Command line to edit::

    python pogs.py $inputs 3 -v

* $inputs : do not modify ; it is a shell variable storing inputs files names
* The 2d argument (default : 3) is the minimal number of species authorized in an orthogroup
* -v is the 'verbose' option (default : activated)
* -p is the 'paralogs' option (default : deactivated)
   
~~~~~~~~~~
BlastAlign
~~~~~~~~~~

Command line to edit::

    BlastAlign+ -i $n -m 95 -n T -s 0

* -i $n : do not modify ; it is the input file
* -m 95 is the proportion of gaps allowed in any sequence in the final alignement
* -n T : Keep T to retain original sequences names in the output file (possible values : T/F)
* -s 0 : is used to set a reference sequence ; 0 means that the option is deactivated here.
   
~~~~~~~~~~
CDS_Search
~~~~~~~~~~

Command lines to edit::

    python S01_find_orf_on_multiple_alignment.py code_universel_modified.txt 50 list_files
    python S02_remove_too_short_bit_or_whole_sequence.py 3 oui 50 15
    python S03_remove_site_with_not_enough_species_represented.py 3 50

* 1st script
  * 1st argument (default : 50) is the minimal length of the CDS (in amino-acids)
  * list_files : do not modify : it contains the list of all input files names
* 2d script
  * 1st argument (default : 3) is the minimal number of species in each orthogroup
  * 2d argument (default : 'oui') means the script takes Methionine into account for the search of CDS (possible values : oui/non)
  * 3d argument (default : 50) is the minimal length of the CDS (in amino-acids)
  * 15
* 3d script
  * 1st argument (default : 3) is the minimal number of species in each orthogroup
  * 2d argument (default : 50) is the minimal length of the CDS, in base-pairs (without indels)
   
~~~~~~~~~~~
Concat_Phyl
~~~~~~~~~~~

Command lines to edit::

    python S01_concatenate.py $inputs nucleic list_files
    raxmlHPC -n raxml -s 03_Concatenation_nuc.phy -m GTRGAMMA -p 1234567890 -N 1000 -x 12345 -f a

* Python script
  * $inputs : do not modify ; it is a shell variable storing inputs files names from filter_assemblies
  * 2d argument (default : nucleic) : specifies the format of the fasta files (possible values : nucleic/proteic)
  * 3d argument : list_files : do not modify ; it contains the list of all input files names from cds_search
     
* raxml :
  * -n : prefix for output files names
  * -s : input file from the python script. Do not modify.
  * -m : Substitution Model
  * -p : random seed for the parsimony inferences
  * -N : number of bootsrap runs
  * -x : rapid bootsrap random seed
  * -f : Algorithm to execute
     
~~~~~~~~
Mutcount
~~~~~~~~

Command lines to edit::

    python S01a_codons_counting.py outputs_phylogeny/03_Concatenation_nuc.fas 'write,here,species,to,count' 'write,here,species,to,resample' 1000 1000

* 1st argument : outputs_phylogeny/03_Concatenation_nuc.fas : do not modify
* 2d argument : 'write,here,species,to,count' is the list of species for countings: Replace those by comma-separated species abbreviated names (ex : Ac,Ap,Am,Pf)
* 3d argument : 'write,here,species,to,resample' is the list of species for resampling: Replace those by comma-separated species abbreviated names (ex : Pg,Pp,Ps,Pi)
* 4th argument : length (in amino-acids) of each resample
* 5th argument : number of resampling iterations

~~~~~~
codeML
~~~~~~

There is no command to edit, but you'll have to set up all the parameters of the configuration file 'codeml_model.ctl'.

.. warning:: Do NOT edit the three first lines : there are automatically edited by the shell script with the appropriate files names

.. code-block:: none

      seqfile = fastaAlignementFile * sequence data file name
      outfile = out_codeml * main result file name
     treefile = newickTreeFile * tree structure file name

Then, you can set all the standards and advanced parameters. The evolutionary models can be chosen with the standards parameters 'model' and 'NSsites' : the different combinations allow to choose between branch models, sites models, and branch-sites models :

.. code-block:: none

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


The config file provides a quick description of each advanced parameter but you can read more at http://abacus.gene.ucl.ac.uk/software/pamlDOC.pdf

.. warning:: The file called *codeml_model.ctl* has to be edited with appropriate parameters for each model computation. A copy of this file is made and store in the output directory. If you delete this model file accidentally, you can create a new one by copy-pasting the example file on `the codeML documentation <CodeML.html#the-codeml-config-file>`_.

.. topic:: Run the tool
  
   Run the tool by typing **qsub codeml.sh 'model_identifier'**, where 'model_identifier' is any name you want, used to create a dedicated directory result. By doing like that, you will be able to run several model on the same results and store the results easily.

Back to `main page <index.html>`_.
