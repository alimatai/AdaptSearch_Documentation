*************************
Visualization with Python
*************************

Heatmaps
========

Overview
--------

This tool takes as input csv files from MutCount in concatenated mode, which contains counts and frequencies of codons, amino-acids, and amino-acids types for each studied species. It calls the Python module Bokeh to build heatmaps on counts and frequencies. Species are in lines and ordered according to their positions in the phylogenetic tree computed by RAxML (ConcatPhyl tool), and codons/amino-acids/amino-acids types are in columns. Codons and amino-acids are ordered according to their type ('aromatics','charged','polar','unpolar').

.. note:: Dots colors map the values of the counts/frequencies, and dots sizes map the pvalue significance ( small : significantly smaller, medium : not significantly different, big : significantly higher)

.. note:: There is an unfinished Galaxy wrapper for this script

Usage
-----

.. code-block:: none

   # command line :
   python heatmap_bokeh.py <csv_file_with_from_MutCount_Concatenated> <counts/frequencies/all> <tree_from_Concatphyl> 
   # example
   python heatmap_bokeh.py aa_freqs.csv all tree.nwk

   # The csv file is one of the following outputs from MutCount in concatenated mode :
   - codons_freqs.csv
   - aa_freqs.csv
   - aa_types_freqs.csv
   All these files contains counts, frequencies, and p-values for each codon/amino-acid/amino-acid type for each species

   # The second argument let the user choose what to plots : 'counts', 'frequencies', or both ('all', which will return two files).

   # The tree is used to order the lines (species) of the Heatmap in order to, later, join the tree representation with the heatmap.

.. image:: _static/frequencies_of_amino-acids.png
   :align: center

Code documentation
------------------

.. py:function:: makeLineOrderFromTree(tree)

   :param tree: a tree in newick format
   :type tree: file
   :return: the species names list extract from the tree
   :rtype: list of Strings

This function is used to have heatmap lines labels

.. py:function:: makeHeatmap(df, what, on_what, colors, range_x, range_y, width, height)

   :param df: an already stacked pandas dataframe
   :type df: pandas DataFrame
   :param what: indicates the column what is being plotted : 'counts' or 'frequencies'
   :type what: String
   :param on_what: indicates the type of data : 'codons', 'amino-acids', 'amino-acids-types'
   :type on_what: String
   :param colors: hexadecimals colors
   :type colors: list of Strings
   :param range_x:
   :type range_x: list of Strings
   :param range_y:
   :type range_y: list of Strings
   :param width: widht of the heatmap
   :type width: int
   :param height: height of the heatmap
   :type height: int

This is the function building the heatmap ; The parameters are set by the main() function and vary according to what is being plotted (codons, amino-acids, amino-acids types, frequencies, counts)

Histograms
==========

Overview
--------

Usage
-----

Code documentation
------------------

Conda environment
=================

I used a conda environment (with Python 2.7) to run these scripts :

.. code-block:: none

   # Install bokeh
   conda install -c bokeh bokeh

   # You'll maybe have to install as well :
   conda install -c bokeh selenium
   conda install -c conda-forge phantomjs
   conda install -c anaconda pillow

   # Pandas and numpy
   conda install -c anaconda numpy 
   conda install -c anaconda pandas

Back to `main page <index.html>`_.