Usercases
=========

*Helicobacter pylori* A45 total methylome analysis
--------------------------------------------------

In this exmpale, we will use Snapper for the total methylaome analysis of *Helicobacter pylori* A45 strain.

The running command::

    (snapper) $ snapper -sample_fast5dir A45_multi -control_fast5dir A45_wga_multi -reference assembly_A45.fna -threads 16 -target_chr NZ_CP053256.1 -max_motifs 30 -outdir Results_A45  

Here we set ``-max_motifs`` parameter to be 30 since *H. pylori* is known to have a huge number of different R-M systems (up to 30).
In addition, we specified ``-target_chr`` parameter to analyze only the main chromosome.

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
    Forward strand signals processing...
    Getting difsignals...
    NZ_CP053256.1: 578090 out of 912990...
    ...


Since all 11-mers that have significant signal shift have been extracted, they are written to the ``passed_motifs_{}.fasta`` file.

The next stage is a greedy motif extraction. In the console output, we can see the motif variant extracted on each algorithm iteration.
For each motif, the algorithm checks if this motif should be merged with some of the motifs found on the previous iterations, which also can bee seen 
in the console output.

When the algorithm extracts the desired number of motifs (in our case it equals 30) or reaches the limit of confidence level (in our case we used the default value equals 1000)
, it stops, and all the extracted motifs are written to the ``final_motifs_{}.fasta`` file. You can see the file that we obtained for *H. pylori* A45 below.

.. code:: console

    >MOTIF_1 conflevel=28467.90477575676
    NCATGN
    >MOTIF_2 conflevel=24296.043882758688
    NGCGCN
    >MOTIF_3 conflevel=19729.375952752132
    NTGCAN
    >MOTIF_4 conflevel=15958.146988566323
    NGAACN
    >MOTIF_5 conflevel=15606.378760571615
    NGATCN
    >MOTIF_6 conflevel=13767.041758068699
    NGGCCN
    >MOTIF_7 conflevel=13539.697412362642
    NCCAGN
    >MOTIF_8 conflevel=14293.92147535083
    NGAHTCN
    >MOTIF_9 conflevel=13870.004827659663
    TGCAAN
    >MOTIF_10 conflevel=13544.634857307088
    NCCATCN
    >MOTIF_11 conflevel=11625.786500468377
    TCNNGAN
    >MOTIF_12 conflevel=10270.257862292823
    NGGGGAN
    >MOTIF_13 conflevel=9984.258291732831
    NTCNGAN
    >MOTIF_14 conflevel=9576.171399773017
    ATTAATN
    >MOTIF_15 conflevel=7988.647089667472
    NGANTC
    >MOTIF_16 conflevel=5202.175232607189
    NGGAGAN
    >MOTIF_17 conflevel=5764.404826357409
    GTNNACN
    >MOTIF_18 conflevel=3194.781954799198
    NTCGAN


The authors recommend to check the motif correctness mannually in cases when its confidence level lower than 3000. In this particular case, all the extracted motifs had a sufficient confidence level,
so, they can be used as is. 

*Helicobacter pylori* A45 native vs mutant analysis
---------------------------------------------------

In the previous example, we generated a list of potential *H.pylori* A45 methylation sites, and few of them has not been described earlier (such as CCAG, GAAC and GGRGA).

In this example, we will use Snapper for the analysis of *H. pylori* A45 mutant knocked-out on a gene encoding a methyltransferase with unknown specificity in order to confirm the 
a new MTase with experimentally.
So, in contrast with previous case, we expect to see only one motif that has a significant signal shift in comaprison with thw ild type.

The running command::

    (snapper) $ snapper -sample_fast5dir A45_multi -control_fast5dir A45_newmut_multi -reference A45.fna -outdir Results_A45_newmut 

