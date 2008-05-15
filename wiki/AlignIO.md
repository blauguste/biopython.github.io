---
title: AlignIO
permalink: wiki/AlignIO
layout: wiki
---

This page describes Bio.AlignIO, a new multiple sequence Alignment
Input/Output interface for BioPython which is currently only in our
source code repository. It should be included in the next release of
Biopython.

Aims
----

You may already be familiar with the [Bio.SeqIO](SeqIO "wikilink")
module which deals with files containing one or more sequences
represented as [SeqRecord](SeqRecord "wikilink") objects. The purpose of
the SeqIO module is to provide a simple uniform interface to assorted
sequence file formats.

Similarly, Bio.AlignIO deals with files containing one or more sequence
alignments represented as Alignment objects. Bio.AlignIO uses the same
set of functions for input and output as in Bio.SeqIO, and the same
names for the file formats supported.

Note that the inclusion of Bio.AlignIO does lead to some duplication or
choice in how to deal with some file formats. For example, Bio.AlignIO
and Bio.Clustalw will both read sequences from Clustal files - but
Bio.Clustalw also includes a command line wrapper to call the program.

My vision is that for reading or writing sequence alignments you should
try Bio.AlignIO as your first choice. In some cases you may only care
about the sequences themselves, in which case try using
[Bio.SeqIO](Bio.SeqIO "wikilink") on the alignment file directly. Unless
you have some very specific requirements, I hope this should suffice.

Peter

File Formats
------------

This table lists the file formats that Bio.SeqIO can read and write. The
format name is a simple lowercase string. Where possible we use the same
name as [BioPerl's
SeqIO](http://www.bioperl.org/wiki/HOWTO:SeqIO#Formats) and
[EMBOSS](http://emboss.sourceforge.net/docs/themes/SequenceFormats.html).

| Format name | Reads | Writes  | Notes                                                                                                                                                                                              |
|-------------|-------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| fasta       | Yes   | Yes     | This refers to the input file format introduced for Bill Pearson's FASTA tool, where each record starts with a "&gt;" line. Note that storing more than one alignment in this format is ambiguous. |
| clustal     | Yes   | Yes     | See also Bio.Clustalw for calling the command line tool.                                                                                                                                           |
| emboss      | Yes   | Soon... | The EMBOSS simple/pairs alignment format.                                                                                                                                                          |
| nexus       | Yes   | No      | Also known as PAUP format. Uses Bio.Nexus                                                                                                                                                          |
| phylip      | Yes   | Yes     | Truncates names at 10 characters.                                                                                                                                                                  |
| stockholm   | Yes   | Yes     | Also known as PFAM format.                                                                                                                                                                         |
||

In addition, you can store the (gapped) sequences from an alignment in
any of the [file formats supported by
Bio.SeqIO](SeqIO#File_Formats "wikilink"). The most common example of
this is storing multiple alignments in the simple fasta format. However,
storing more than one alignment in a single such file is ambiguous - see
the section below on alignment input.

Alignment Input
---------------

As in [Bio.SeqIO](SeqIO "wikilink"), there are two functions for
alignment input. These are **Bio.AlignIO.read()** for when the file
contains one and only one alignment, and the more general
**Bio.AlignIO.parse()** when the file may contain multiple separate
alignments.

Both these functions have two required arguments, a file handle and a
file format. As with [Bio.SeqIO](SeqIO "wikilink"), Biopython insists
that you explicitly give the expected file format, rather than
attempting to guess this based on the filename or contents. The file
format is specified as a lower case string, see the table above.

As an example, we'll look at a PFAM *seed alignment* for the Fibrinogen
gamma chain [PF09395
Fib\_gamma](http://pfam.sanger.ac.uk/family?acc=PF09395). At the time of
writing, this contained 14 sequences with an alignment length of 77
amino acids, and is shown below in the PFAM or Stockholm format:

    # STOCKHOLM 1.0
    #=GS Q7ZVG7_BRARE/37-110  AC Q7ZVG7.1
    #=GS Q6X871_SCAAQ/1-77    AC Q6X871.1
    #=GS O02676_CROCR/1-77    AC O02676.1
    #=GS Q6X869_TENEC/1-77    AC Q6X869.1
    #=GS FIBG_HUMAN/40-116    AC P02679.3
    #=GS O02689_TAPIN/1-77    AC O02689.1
    #=GS O02688_PIG/1-77      AC O02688.1
    #=GS O02672_9CETA/1-77    AC O02672.1
    #=GS O02682_EQUPR/1-77    AC O02682.1
    #=GS Q6X870_CYNVO/1-77    AC Q6X870.1
    #=GS FIBG_RAT/40-116      AC P02680.3
    #=GS Q6X866_DROAU/1-76    AC Q6X866.1
    #=GS O93568_CHICK/40-116  AC O93568.1
    #=GS FIBG_XENLA/38-114    AC P17634.1
    Q7ZVG7_BRARE/37-110          GFGTYCPTTCGVADYLQRYKPDMDKKLDDMEQDLEEIANLTRGAQDKVVYLK---DSEAQAQKQSPDTYIKKSSNML
    Q6X871_SCAAQ/1-77            RFGSYCPTTCGIADFLSTYQATVDKDLQTLEDILSQAENKTMEAKELVKAIQVSYLPEDPARPNRVELATKDSKKMM
    O02676_CROCR/1-77            RFGSYCPTTCGIADFLSTYQTGVXNDLRTLEDLLSGIENKTSEAKELIKSIQVSYNPNEPPKPNTIVSATKDSKKMM
    Q6X869_TENEC/1-77            RFGSYCPTTCGIADFLSTYQGSIDKDLQTLEDILNQVENKTXEASELIKSIQVSYNPDEPPRPNMIEGATQKSKKML
    FIBG_HUMAN/40-116            RFGSYCPTTCGIADFLSTYQTKVDKDLQSLEDILHQVENKTSEVKQLIKAIQLTYNPDESSKPNMIDAATLKSRKML
    #=GS FIBG_HUMAN/40-116    DR PDB; 1qvh L;14-45 
    #=GS FIBG_HUMAN/40-116    DR PDB; 1fza C;88-90 
    #=GS FIBG_HUMAN/40-116    DR PDB; 1fzb C;88-90 
    #=GS FIBG_HUMAN/40-116    DR PDB; 1fzb F;88-90 
    #=GS FIBG_HUMAN/40-116    DR PDB; 1qvh I;14-45 
    #=GS FIBG_HUMAN/40-116    DR PDB; 1fza F;88-90 
    #=GR FIBG_HUMAN/40-116    SS CCXCXBXXHHHHHHHHHHHHHHHHHHHHHHHXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX-CC
    O02689_TAPIN/1-77            RFGSYCPTTCGIADFLSTYQTXVDKDLQVLEDILNQAENKTSEAKELIKAIQVRYKPDEPTKPGGIDSATRESKKML
    O02688_PIG/1-77              RFGSYCPTMCGIAGFLSTYQNTVEKDLQNLEGILHQVENKTSEARELIKAIQISYNPEDLSKPDRIQSATKESKKML
    O02672_9CETA/1-77            RFGSYCPTTCGVADFLSNYQTSVDKDLQNLEGILYQVENKTSEARELVKAIQISYNPDEPSKPNNIESATKNSKRMM
    O02682_EQUPR/1-77            RFGSYCPTTCGIADFLSNYQTSVDKDLQDFEDILHRAENQTSEAEQLIQAIRTSYNPDEPPKTGRIDAATRESKKMM
    Q6X870_CYNVO/1-77            RFGSYCPTTCGIADFLSTYQTKVDEDLQNLEDILYRVENRTSEAKELIKAIQVDYNPGEPPKQSVTEGATQNAKKMV
    FIBG_RAT/40-116              RFGSYCPTTCGISDFLNSYQTDVDTDLQTLENILQRAENRTTEAKELIKAIQVYYNPDQPPKPGMIEGATQKSKKMV
    Q6X866_DROAU/1-76            RFGSYCPTTCGIADFLNKYQTTIDQDLRHMEETLRDIDNKTAESTLLIQKIQIGQTPDPRPQ-NVIGDVTQKSRKMI
    O93568_CHICK/40-116          RFGSYCPTTCGIADFFNKYRLTTDGELLEIEGLLQQATNSTGSIEYLIQHIKTIYPSEKQTLPQSIEQLTQKSKKII
    #=GS O93568_CHICK/40-116  DR PDB; 1m1j F;14-90 
    #=GS O93568_CHICK/40-116  DR PDB; 1m1j C;14-90 
    #=GR O93568_CHICK/40-116  SS CCEEEEE-CCCCCCCCCCCCCHHHCCCCCHHHHHHHHHHHHHHHCCCCCCHHHHS-SSTT--SS-HHHHHHHHHHHH
    FIBG_XENLA/38-114            RFGEYCPTTCGISDFLNRYQENVDTDLQYLENLLTQISNSTSGTTIIVEHLIDSGKKPATSPQTAIDPMTQKSKTCW
    #=GC SS_cons                 CCECEEE-CCCCCCCCCCCCCHHHCCCCCHHHHHHHHHHHHHHHCCCCCCHHHHS-SSTT--SS-HHHHHHHHHHCC
    #=GC seq_cons                RFGSYCPTTCGIADFLSsYQssVDcDLQsLEsILpplEN+ToEAc-LIKuIQlsYsP--ss+PstI-uATpcSKKMl
    //

You will notice that there is plenty of annotation information here,
including accession numbers for each sequence and also some PDB database
cross-references and secondary structure information for the human and
chick fibrinogen proteins.

This file contains a single alignment, so we can use the
**Bio.AlignIO.read()** function to load it in Biopython. Let's assume
you have downloaded this alignment from Sanger, or have copy and pasted
the text above, and saved this as a file called **PF09395\_seed.sth** on
your computer. Then in python:

``` python
from Bio import AlignIO
alignment = AlignIO.read(open("PF09395_seed.sth"), "stockholm")
print "Alignment length %i" % alignment.get_alignment_length()
for record in alignment :
    print record.seq, record.id
```

That should give:

    Alignment length 77
    GFGTYCPTTCGVADYLQRYKPDMDKKLDDMEQDLEEIANLTRGAQDKVVYLK---DSEAQAQKQSPDTYIKKSSNML Q7ZVG7_BRARE/37-110
    RFGSYCPTTCGIADFLSTYQATVDKDLQTLEDILSQAENKTMEAKELVKAIQVSYLPEDPARPNRVELATKDSKKMM Q6X871_SCAAQ/1-77
    RFGSYCPTTCGIADFLSTYQTGVXNDLRTLEDLLSGIENKTSEAKELIKSIQVSYNPNEPPKPNTIVSATKDSKKMM O02676_CROCR/1-77
    RFGSYCPTTCGIADFLSTYQGSIDKDLQTLEDILNQVENKTXEASELIKSIQVSYNPDEPPRPNMIEGATQKSKKML Q6X869_TENEC/1-77
    RFGSYCPTTCGIADFLSTYQTKVDKDLQSLEDILHQVENKTSEVKQLIKAIQLTYNPDESSKPNMIDAATLKSRKML FIBG_HUMAN/40-116
    RFGSYCPTTCGIADFLSTYQTXVDKDLQVLEDILNQAENKTSEAKELIKAIQVRYKPDEPTKPGGIDSATRESKKML O02689_TAPIN/1-77
    RFGSYCPTMCGIAGFLSTYQNTVEKDLQNLEGILHQVENKTSEARELIKAIQISYNPEDLSKPDRIQSATKESKKML O02688_PIG/1-77
    RFGSYCPTTCGVADFLSNYQTSVDKDLQNLEGILYQVENKTSEARELVKAIQISYNPDEPSKPNNIESATKNSKRMM O02672_9CETA/1-77
    RFGSYCPTTCGIADFLSNYQTSVDKDLQDFEDILHRAENQTSEAEQLIQAIRTSYNPDEPPKTGRIDAATRESKKMM O02682_EQUPR/1-77
    RFGSYCPTTCGIADFLSTYQTKVDEDLQNLEDILYRVENRTSEAKELIKAIQVDYNPGEPPKQSVTEGATQNAKKMV Q6X870_CYNVO/1-77
    RFGSYCPTTCGISDFLNSYQTDVDTDLQTLENILQRAENRTTEAKELIKAIQVYYNPDQPPKPGMIEGATQKSKKMV FIBG_RAT/40-116
    RFGSYCPTTCGIADFLNKYQTTIDQDLRHMEETLRDIDNKTAESTLLIQKIQIGQTPDPRPQ-NVIGDVTQKSRKMI Q6X866_DROAU/1-76
    RFGSYCPTTCGIADFFNKYRLTTDGELLEIEGLLQQATNSTGSIEYLIQHIKTIYPSEKQTLPQSIEQLTQKSKKII O93568_CHICK/40-116
    RFGEYCPTTCGISDFLNRYQENVDTDLQYLENLLTQISNSTSGTTIIVEHLIDSGKKPATSPQTAIDPMTQKSKTCW FIBG_XENLA/38-114

Alignment Output
----------------

As in [Bio.SeqIO](SeqIO "wikilink"), there is a single output function
**Bio.AlignIO.write()**.

File Format Conversion
----------------------

Suppose you have a file containing PHYLIP alignment(s) that you want to
convert into the PFAM/Stockholm format:

``` python
from Bio import AlignIO

input_handle = open("example.phy", "rU")
output_handle = open("example.sth", "w")

alignments = AlignIO.parse(input_handle, "phylip")
AlignIO.write(alignments, output_handle, "stockholm")

output_handle.close()
input_handle.close()
```

By changing the format strings, that code could be used to convert
between any supported file formats.