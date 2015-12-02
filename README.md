# Falcon2Fastg 

This software converts the results of PacBio assembly using [FALCON](https://github.com/PacificBiosciences/FALCON), to a [FASTG](http://fastg.sourceforge.net/) graph that can be visualized using [Bandage](http://rrwick.github.io/Bandage/).

### Usage

    python Falcon2Fastg.py [--only-output=reads|contigs]

Run it in the output directory of FALCON assembly (2-asm-falcon). It needs 
the following input files:

* preads4falcon.fasta

* sg_edges_list 

* utg_data (if `--only-output` is unset, or set to `contigs`)

* ctg_paths (if `--only-output` is unset, or set to `contigs`)

* p_ctg.fa (if `--only-output` is unset, or set to `contigs`)



### Dependencies :

Biopython (available at http://biopython.org/wiki/Download)

pyfaidx (available at https://github.com/mdshw5/pyfaidx)

Quick installation of dependencies:

    pip install biopython pyfaidx  # add --user if you don't have root

### Output : 

The output of the tool is two FASTG files (``reads.fastg`` and ``contigs.fastg``) that can be opened with 
[Bandage](http://rrwick.github.io/Bandage/).

![Alt text](/img/Falcon2Fastg_after_bandage.png?raw=true "Falcon2Fastg after Bandage")


Above is a sample Bandage visualization of a ``reads.fastg`` file generated by 
Falcon2Fastg from a FALCON assembly (a plant mitochondrial genome).

* Each node is a read, and each node is represented as a colored strip 
(colors are random)
* Edges represent the overlaps between reads found by FALCON (better viewed in 
the zoomed-in image below) 
* Only the edges used in the string graph ("G" flagged in sg_edges_list) are 
used by Falcon2Fastg to produce the output file.



Zooming in on a smaller set of nodes shows the edges in black, connecting the colored nodes :


![Alt text](/img/zoomed_mito.png?raw=true "Falcon2Fastg zoom with Bandage")




For benchmarking, Falcon2Fastg was run on the preads4falcon.fasta and 
sg_edges_list file produced by the E.coli test dataset provided with the Falcon
 install. Instructions on obtaining the dataset are here : 
https://github.com/PacificBiosciences/FALCON/wiki/Setup:-Complete-example  

Execution of Falcon2Fastg took 2 minutes on a desktop computer (size of ``preads4falcon.fasta``: 449 MB).

The figure below represents a visualization of this E. coli data.  

![Alt text](/img/ecoli_Gnodes.png?raw=true "Ecoli 'G' edges fastg after Bandage")


### Contigs visualization 

Falcon2Fastg can also be used to visualize the contigs produced by FALCON, and overlaps between them. The contig graph is created in ``contigs.fastg``. By default, Falcon2Fastg will output this file. You can choose that it outputs only the reads graph using the ``--only-output=reads`` parameter.


To test this visualization mode, we assembled Drosophila melanogaster reads available at 
(https://github.com/PacificBiosciences/DevNet/wiki/Drosophila-sequence-and-assembly). The FALCON assembly parameters were not optimized, and the final p_ctgs.fa file had 642 contigs with total length ~27 Mbp.  

Execution of Falcon2Fastg took 5 minutes on a desktop computer (size of ``preads4falcon.fasta``: 2.1 GB).

The figure below is the visualization of these D. mel. contigs (colors are random)

![Alt text](/img/Dmel_ctgs.png?raw=true "D. mel. contigs after Bandage")


### Read density (approximate read coverage)

Bandage provides a way to visualize k-mer coverage, as reported by the assembler. As Falcon is a string graph assembler, it does not report such information. Ideally, to compute the coverage of a contig, one would need to re-map the reads back to the assembled contigs. Here, we report a more simple metric that is easy to compute from the output of Falcon. 

*Read density* is calculated as (sum of length of all reads used in contig / 
length of contig). We believe that variation in read density reflects variation of coverage;

The figure below is a schematic of read density. The blue arrows represent reads that were used by Falcon to create the red (resp. black) contig. The contig above (black) has fewer reads within it. Its read density is around 2.0
The contig below (red) and has more reads within it. Its read density is around 5.0
 

![Alt text](/img/read_density_schematic.png?raw=true "Read Density schematic")


The figure below is the visualization of the same D. mel. contigs with colors representing read density. 

![Alt text](/img/Dmel_ctgs_rd.png?raw=true "D. mel. contigs after Bandage")

Zooming in shows that bright red represents higher density (6.0x).
Contigs colored black have a lower read density (2.0x) 

![Alt text](/img/Dmel_ctgs_rd_zoom.png?raw=true "D. mel. contigs after Bandage")

### Memory Warning 

The pyfaidx module is used to read an entire FASTA file into memory. 
If the size of your preads4falcon.fasta is greater than the amount of available
 RAM, it is advisable to run this computation on a server with greater
available memory. 



### Caveats : 
 
- Reads within "contained" unitigs are not used in the calculation of Read density. 

- Read density is calculated by dividing total length of all reads in the contig 
by length of each contig (obtained from ctg_paths). Depending on the orientation, 
Falcon ignores either the first read or the last read while reporting a contig. 
Due to this, in the contigs.fastg file, the forward and rev_comp entries might 
have different read_densities and different lengths. 

Any large differences are mostly restricted to short contigs, when one very 
long read at either extremity can affect the length of the contig. 

- Read density is set to "1" for entries in reads.fastg, as this measure is 
only relevant for contigs.fastg

### Testing :

Please see the test/ directory for a small example dataset and output

FALCON can be installed following the instructions here : 
https://github.com/PacificBiosciences/FALCON/wiki/Setup:-Complete-example

## Other tools

Additional tools for visualizing read overlap can be found in the utils directory.
Please consult utils/README.md for details


## License 

This content is released under [MIT License](https://opensource.org/licenses/MIT). Please see LICENSE.md for details.


## Authors

Primary author : Samarth Rangavittal, The Pennsylvania State University
                 (szr165@psu.edu)
                
Rayan Chikhi, University of Lille 1   

Jean-Stéphane Varré, University of Lille 1

