Usage
=====

.. _installation:

Installation
------------

To use Snapper, first install it using pip::

   (base) $ conda create -n snapper python=3.7
   (base) $ conda activate snapper
   (snapper) $ conda install -c bioconda ont-fast5-api ont-tombo
   (snapper) $ pip install snapper-ont

We recommend to use python 3.7 because later python versions might be incompatible 
with inner biopython dependencies.

Usage
----------------

The Snapper pipeline is designed to perform only comparative analysis 
and requires two samples to be sequenced. In most cases, these samples are native DNA and control DNA obtained after whole-genome amplification (WGA), 
but in general it is not a constrain. Thus, the algorithm might be used to compare two native DNA dataset, where the first has been extracted from some wild type bacteria, 
and the second from a mutant. Such an example is considered in details in the :doc:`usercases` section.


Suppose that we have :code:`fast5_native` folder containing single-fast5 files for the native sample, :code:`fast5_wga` for the control sample, 
and :code:`reference.fna` with the genome of the object of our interest. 

Firstly, the fast5 files should be resquiggled using `Tombo <https://nanoporetech.github.io/tombo/index.html>`_ and converted to the multifast5-format using `ont-fast5-api <https://github.com/nanoporetech/ont_fast5_api>`_::

   (snapper) tombo resquiggle fast5_native reference.fna
   (snapper) tombo resquiggle fast5_wga reference.fna
   (snapper) single_to_mullti_fast5 -i fast5_native -s fast5_multi_native
   (snapper) single_to_mullti_fast5 -i fast5_wga -s fast5_multi_wga


Before the resquiggling command, ensure that basecalling data have been added to the fast5 files (see `tombo preprocess annotate_raw_with_fastqs <https://nanoporetech.github.io/tombo/resquiggle.html>`_ command).
The authors do not recommend to change the batch-size parameter (``-n``, ``--batch-size``) while running ``single_to_multi_fast5`` command since the default value (4000 reads per batch) seems reasonable in terms of efficiency. 

A typical Snapper running command::

   (snapper) snapper -sample_fast5dir fast5_multi_native -control_fast5fir fast5_mutli_wga -reference reference.fna

Optional parameters:

* :code:`-ks_t`         
   The threshold value of -log(p-value) in the Kolmogorov-Smirnov test (default is 5). In general, the lower this value, the higher the algorithm sensitivity,
   but the authors recommend to tune this parameter only in cases when the coverege of either native or control sample is less than 20 per position,
   since the algorithm performs inner k-mers coverage balancing while computing statistcs values.
* :code:`-outdir`       
   Output directory name. By default, the output directory is named :code:`Results_yyyy_mm_dd_tttttt` according to the run data and time, for example :code:`Results_2022_09_02_180002`.
* :code:`-n_batches`    
   Number of parsed fast5 batches. The default value equals the number of multifast5 files passed to the algorithm, but not greater than 100. 
* :code:`-threads`      
   Number of threads used (default is 8)
* :code:`-max_motifs`   
   The maximum expected number of motifs that the algorithm will try to extract. The resulting number of motifs might be lower.
* :code:`-min_conf`     
   The minimal confidence (chi-square statistics) value. Default is 1000.
* :code:`-target_chr`   
   Target chromosome name. By default, all sequences available in the input reference fasta file are processed independently.


Output files explanation
------------------------

For each contig and each strand, the analysis is performed independently, so, in the output folder you can see a few resulting files for each contig+strand variant.
Let's consider forward strand of ``contig_1`` contig. The following files are generated for this particular combination:

* ``passed_motifs_forward_contig_1.fasta``
   This file contains all 11-mers that have a significant signal shift between the native and control samples.
* ``final_motifs_forward_contig_1.fasta``
   This file contains a sorted list of all potential methylation motifs extracted by a greedy algorithm implemented in Snapper. For each extracted motif, the confidence value is given in the header.
   For motifs with confidence level lower than 3000, the author recommend to check their correctness manually (see :doc:`usercases`)
* ``seq_iter/forward_contig_1/`` folder 
   In this folder the greedy algorithm saves sets of unexplained 11-mer sequences on each greedy interation. Formally, the ``seqs_iter_1.fasta`` is identical to ``passed_motifs_forward_contig_1.,fasta`` since 
   the all passed 11-mers are unexplained on the first iteration.
* ``plots_forward_contig_1``
   This folder contains signal distribution plots for each extracted methylation motif.
