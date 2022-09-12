Usercases
=========

*Helicobacter pylori* J99 total methylome analysis
--------------------------------------------------

In this exmpale, we will use Snapper for the total methylaome analysis of *Helicobacter pylori* J99 strain.

The running command::

    (snapper) $ snapper -sample_fast5dir J99_multi -control_fast5dir J99_wga_multi -reference assembly_J99.fna -threads 16 -target_chr contig_1_pilon_pilon_pilon -max_motifs 30 -outdir Results_J99  

Here we set ``-max_motifs`` parameter to be 30 since *H. pylori* is known to have a huge number of different R-M systems (up to 30).
In addition, we specified ``-target_chr`` parameter and passed the name of the main chromosome, because in this specific case the coverege of plasmid was not sufficient for analysis.




*Helicobacter pylori* A45 native vs mutant analysis
---------------------------------------------------

In this example, we will use Snapper for the analysis of *H. pylori* A45 mutant knocked-out on a gene encoding a methyltransferase with unknown specificity.
So, in contrast with previous case, we expect to see only one motif that has a significant signal shift in comaprison with thw ild type.

The running command::

    (snapper) $ snapper -sample_fast5dir A45_multi -control_fast5dir A45_newmut_multi -reference A45.fna -outdir Results_A45_newmut 

