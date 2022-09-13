Usercases
=========

*Helicobacter pylori* J99 total methylome analysis
--------------------------------------------------

In this exmpale, we will use Snapper for the total methylaome analysis of *Helicobacter pylori* J99 strain.

The running command::

    (snapper) $ snapper -sample_fast5dir J99_multi -control_fast5dir J99_wga_multi -reference assembly_J99.fna -threads 16 -target_chr contig_1_pilon_pilon_pilon -max_motifs 30 -outdir Results_J99  

Here we set ``-max_motifs`` parameter to be 30 since *H. pylori* is known to have a huge number of different R-M systems (up to 30).
In addition, we specified ``-target_chr`` parameter and passed the name of the main chromosome, because in this specific case the coverege of the *H. pylori* plasmid was not sufficient for analysis.

The first stage of the pipeline is raw signals collecting. It might up to one hour depending on the dataset size. 
On average, a dataset consisting of 40-50 multifast5 batches is processed not longer than in 15-20 minutes.

.. code:: console

    Sample data collecting...
    Batch 1 out of 40...
    Batch 2 out of 40...
    # and so on...

    
    Control data collecting...
    Batch 1 out of 40...
    Batch 2 out of 40...
    # and so on...


Next, the algorithm performs the Kolmogorov-Smirnov test to compare signal distributions for each 11-mer presented in the reference genome.

.. code:: console

Since all 11-mers that have significant signal shift have been extracted, they are written to the ``passed_motifs_{}.fasta`` file.

The next stage is a greedy motif extraction. In the console output, we can see the motif variant extracted on each algorithm iteration.
For each motif, the algorithm checks if this motif should be merged with some of the motifs found on the previous iterations, which also can bee seen 
in the console output.

When the algorithm extracts the desired number of motifs (in our case it equals 30) or reaches the limit of confidence level (in our case we used the default value equals 1000)
, it stops, and all the extracted motifs are written to the ``final_motifs_{}.fasta`` file. You can see the file that we obtained for *H. pylori* J99 below.

.. code:: console

    >MOTIF_1 conflevel=27103.812619615586
    NCATGN
    >MOTIF_2 conflevel=25271.120741238476
    NGCGCN
    >MOTIF_3 conflevel=22858.215544862487
    NGATCN
    >MOTIF_4 conflevel=14881.210142625916
    NGANTCN
    >MOTIF_5 conflevel=13377.13156417874
    NCCGGN
    >MOTIF_6 conflevel=11745.492352209352
    NGCCTAN
    >MOTIF_7 conflevel=9668.218539259307
    NGACAY
    >MOTIF_8 conflevel=8898.54760338392
    NGTCATN
    >MOTIF_9 conflevel=8160.868203159104
    NGAGGN
    >MOTIF_10 conflevel=8378.43839522445
    NACGTN
    >MOTIF_11 conflevel=7872.51406852035
    NCCNNGG
    >MOTIF_12 conflevel=7070.2058154990955
    ATTAATN
    >MOTIF_13 conflevel=5502.44744861762
    NGGNCTAN
    >MOTIF_14 conflevel=5544.476242630171
    NGGWCAAN
    >MOTIF_15 conflevel=5351.005717554389
    NGTACN
    >MOTIF_16 conflevel=4384.303493212003
    NCGACGN
    >MOTIF_17 conflevel=4242.870880053313
    NCGTCGN
    >MOTIF_18 conflevel=3779.062212247213
    NTCGAN
    >MOTIF_19 conflevel=3594.599766259159
    CCTAAN
    >MOTIF_20 conflevel=2770.1124650379484
    GGACGAN
    >MOTIF_21 conflevel=2656.2205060160477
    NGTCACN
    >MOTIF_22 conflevel=1734.1045411693126
    NGTGACN
    >MOTIF_23 conflevel=1718.0747564787976
    GTCNATN
    >MOTIF_24 conflevel=1543.467066776315
    NTACCG
    >MOTIF_25 conflevel=1551.2644823054413
    CGTCGTN
    >MOTIF_26 conflevel=1433.3165247859145
    NTGCCG

The authors recommend to check the motif correctness mannually in cases when its confidence level lower than 3000.



*Helicobacter pylori* A45 native vs mutant analysis
---------------------------------------------------

In this example, we will use Snapper for the analysis of *H. pylori* A45 mutant knocked-out on a gene encoding a methyltransferase with unknown specificity.
So, in contrast with previous case, we expect to see only one motif that has a significant signal shift in comaprison with thw ild type.

The running command::

    (snapper) $ snapper -sample_fast5dir A45_multi -control_fast5dir A45_newmut_multi -reference A45.fna -outdir Results_A45_newmut 

