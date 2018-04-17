****************
Orthogroups_Tool
****************


Overview
********

This tool takes Orthogroups found by OrthoFinder and proceeds to retrieve nucleic sequences back, then write each orthogroups in its own fasta file.

User parameters
===============

The parameter's name are the ones used in the Galaxy's interface.
 #. Orthogroup file : the *Orthogroups.txt* outpuf file from OrthoFinder
 #. Outputs from Filter Assemblies : all the `Filter_Assemblies <Filter_Assemblies.html>`_ outputs, separated by commas
 #. (integer) Minimal number of sequences per orthogroup : smaller orthogroups will not be returned
 #. (-v) Verbose : display a supplementary table with details before the paralogous sequence removal
 #. (-p) Paralogs : if activated, returns the orthogroups before paralogs sequences removal


Code documentation
******************

Algorithm
=========

:: note:: under Galaxy, a first shell script is launched to rewrite correctly TransDecoder sequences' headers in the `Orthogroups.txt` file.

 #. Outputs of `Filter_Assemblies <Filter_Assemblies.html>`_ are used to buid a hashtable between headers and sequences
 #.`Orthogroups.txt` file is parsed and : 

    #. Each line (a line = an orthogroup) is put into a list
    #. Paralogous sequences are removed from the list (only one sequence per species is kept)
    #. Each element of the list is written in a fasta file, with the sequence retrieved from the hashtable
    #. Summary countings are recorded and displayed on the screen.

The code is not fully optimized and the whole orthogroups list is parsed 2-3 times.

Source code
===========

.. todo:: source code docs !

.. py:function:: hashSequences(path)

   :param path: the fasta files outputs of Filter_Assemblies
   :type path: list
   :return: a hashTable between headers (keys) and sequences (values)
   :rtype: dict

.. py:function:: formatAndFilter(orthogroups, mini, nbspecs, hashTable, verbose, paralogs)

   :param orthogroups: 'Orthogroups.txt' file
   :type orthogroups: file
   :param mini: minimal number of species per group (set by the user)
   :type mini: int
   :param nbspecs: Total number of species
   :type nbspecs: int
   :param hashTable: hashtable headers/sequences `returned by hashSequences()`
   :type hashTable: dict
   :param verbose: set by -v option ; activate the display of an intermediate summary table (orthogroups before paralogs removal)
   :type verbose: bool
   :param paralogs: set by -p option ; activate the writing of intermediate output files (orthogroups before paralogs removal)
   :type paralogs: bool
   :return: the total number of orthogroups
   :rtype: int

This is the main function, which creates the orthogroups by parsing the file *Orthogroups.txt* returned by *OrthoFinder_OnlyGroups* and writes output files

formatAndFilter() sub-routines
------------------------------

.. py:function:: writeOutputFile(orthogroup, hashTable, i, naming)

   :param orthogroup: contains all sequences of an orthogroup
   :type orthogroup: list
   :param hashtable: hashtable headers/sequences `returned by hashSequences()`
   :type hashtable: dict
   :param i: used to set a unique filename
   :type i: int
   :param naming: the file name varies according to the value of this parameter (depends on the *paralogs* option)
   :type naming: bool

Used to write an orthogroup in an output fasta file.

.. py:function:: countings(listOrthogroups, nbcols)

   :param listOrthogroups: computed in makeOrthogroups()
   :type listOrthogroups: list of sets of Locus objects
   :param nbcols: the total number species (e.g the number of fasta input files)
   :type nbcols: int
   :return: a summary table containing the number of orthogroups and the number of species per orthogroup.
   :rtype: numpy 2D array

.. py:function:: asFrame(matrix)

   :param matrix: the output of countings()
   :type matrix: numpy 2D array
   :return: the matrix converted in a pandas dataFrame

Back to `main page <index.html>`_.