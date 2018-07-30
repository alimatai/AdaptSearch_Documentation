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

This tool display, on species per species barplots, the results of codons/amino-acids/amino-acids types counts and frequencies computed by The tool MutCount (in concatenated mode)

Usage
-----

.. code-block:: none

   # command line :
   python barplots_bokeh.py <csv_file_with_from_MutCount_Concatenated> <counts/frequencies/both>
   # example
   python scripts/barplots_bokeh.py test-data/aa_freqs.csv both

   # The csv file is one of the following outputs from MutCount in concatenated mode :
   - codons_freqs.csv
   - aa_freqs.csv
   - aa_types_freqs.csv
   All these files contains counts, frequencies, and p-values for each codon/amino-acid/amino-acid type for each species

   # The second argument let the user choose what to plots : 'counts', 'frequencies', or both (which will return two files).

.. image:: _static/Frequencies_on_Ac_amino-acids.png
   :align: center

Code documentation
------------------

.. py:function:: makeValues(df, start, list_sp)

   :param df: a dataframe storing counts and pvalues of several species (MutCount concatenated output)
   :type df: pandas DataFrame
   :param start: indicates the starting line of the dataframe parsing
   :type start: int
   :param list_sp: the list of species names
   :type list_sp: list of Strings
   :returns: specific values of the DataFrame (according to the 'start' param)
   :rtype: dic

   Allows to store in a separate dictionary a specific category of values (counts, frequencies, or pvalues) with all species.

.. py:function:: make_barplot(species, xaxis, dic_yaxis, dic_yaxis_pvalues, what, x_range, width, end_title)

   :param species: A species name
   :type species: String
   :param xaxis: list of elements to plot (index of pandas dataframe)
   :type xaxis: list
   :param dic_yaxis: Values for bars at each x label
   :type y_axis: dict
   :param dic_yaxis_pvalues:
   :type dic_yaxis_pvalues:
   :param what: Specifies what is being plotted ('Counts', 'Frequencies'). Used to build the title and find corresponding values in the pandas dataframe
   :type what: String
   :param x_range: labels categories for the xaxis ( ~= param xaxis, but sorted by amino-acids types)
   :type x_range: dict
   :param width: plot width
   :type width: int
   :param end_title: suffix for the plot title ('codons'/'amino-acids'/'amino-acids-types')
   :type end_title: String

   Main function building barplots on counts or frequencies for each species. Bars are colored according to the pvalue category (significantly lower, unsignificant, significantly higher)


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