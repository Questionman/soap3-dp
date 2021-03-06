2bwt-lib - README
Version 1.0.54
==================

Introduction
------------
2BWT (Bi-directinal BWT) is a full-text index of DNA that can support efficient pattern matching via backward search, forward search, or a mix of backward and forward search (i.e., switch of search direction is allowed). Typically, it takes tens of minutes to build the 2BWT for the human genome (~3 billion bp), and a few microseconds to search the occurrences of a pattern with a few hundred characters.

Installation
------------
1. Un-tar the source code package
% gunzip 2bwt-lib-v1.0.0-x84-64bit.tar.gz
% tar -xvvf 2bwt-lib-v1.0.0-x84-64bit.tar

2. Navigate into the directory with Makefile present
% 2bwt-lib-v1.0.0
% make

Then, you should see a 2bwt-builder program being built. 2bwt-builder is the program which builds 2BWT index from FASTA formatted reference sequence. For usage of this program, please proceed to the next section.
Usage Guide

% ./2bwt-builder <ref sequence>

<ref sequence> is the path to your FASTA formatted DNA sequences. e.g.
% ./2bwt-builder ncbi.genome.fa

In this example, 2bwt-builder will build up a 2BWT index on the sequence ncbi.genome.fa. The output index will be named ncbi.genome.fa.index.*. There should be 16 files with this prefix created.
NOTE: 2bwt-builder operates based on certain number of restrictions and assumptions. See 2bwt-lib-v1.0.0-readme.txt to view them.

NOTE: What is in the index? 
*.bwt -- The BWTs of the reference sequence.
*.fmv -- The auxiliary data structure to support operations on the BWTs.
*.pac -- The reference sequence in 2-bit per character format.
*amb/*.ann -- The information of the different chromosomes/sub-sequence of reference sequence. Also, the position and length of each removed long ambiguous region(N-region).
*.sa -- The Suffix Array
*.lkt -- Lookup table for fast retrieval of SA ranges of any length-k pattern where k is pre-defined in index building time.

Development with 2BWT package
-----------------------------
Though 2bwt-builder is the only runnable program built from the compilation, there are also a handy 2BWT Library that is built together which allows developer to implement different applications with 2BWT package rapidly. Below is a list of functions provided by the 2BWT Library(2BWT-Interface.h) and some brief description of them.

    Index loading/unloading
    -----------------------
    
    > BWTLoad2BWT(indexFilePrefix,saFileNameExtension)
    It loads the 2BWT index from disk into memory
    
    > BWTFree2BWT(idx2BWT)
    It unloads the 2BWT index from memory
    
    Pattern pre-processing
    ----------------------
    
    > BWTConvertPattern(idx2BWT,patternSource,patternLength,patternDestination)
    In order for 2BWT to be able to process the character on your pattern, you might first convert it from DNA character into a coding scheme recognised by 2BWT. This function converts human readable DNA pattern into a 2BWT recognised one. For all BWT search function below, all characters passing in and out them are expected to be "converted" characters.
    
    Pattern matching - incremental by character
    -------------------------------------------
    
    > BWTSARangeInitial(idx2BWT,c,saIndexLeft,saIndexRight)
    BWT search for the SA range of the first character c. Initial values of saIndexLeft and saIndexRight will be overwritten.
    
    > BWTSARangeBackward(idx2BWT,c,saIndexLeft,saIndexRight)
    Incremental BWT backward search for the SA range of cP where P is a pattern having SA range (saIndexLeft,saIndexRight). Initial values of saIndexLeft and saIndexRight will be overwritten.
    
    > BWTSARangeBackward_Bidirection(idx2BWT,c,saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight)
    Incremental Bi-directional BWT backward search for the SA ranges of cP where P is a pattern having SA range (saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight). Initial values of saIndexLeft, saIndexRight, rev_saIndexLeft and rev_saIndexRight will be overwritten.
    
    > BWTSARangeForward_Bidirection(idx2BWT,c,saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight)
    Incremental Bi-directional BWT forward search for the SA range of Pc where P is a pattern having SA range (saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight). Initial values of saIndexLeft, saIndexRight, rev_saIndexLeft and rev_saIndexRight will be overwritten.
    
    > BWTAllSARangesBackward(idx2BWT,saIndexLeft,saIndexRight,resultSaIndexesLeft,resultSaIndexesRight)
    Incremental BWT backward search for all SA ranges of cP where P is a pattern having SA range (saIndexLeft,saIndexRight) for all character c in alphabet set.
    
    > BWTAllSARangesBackward_Bidirection(idx2BWT,c, saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight, resultSaIndexesLeft,resultSaIndexesRight,rev_resultSaIndexesLeft,rev_resultSaIndexesRight)
    Incremental Bi-directional BWT backward search for all SA ranges of cP where P is a pattern having SA range (saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight) for all character c in alphabet set.
    
    > BWTAllSARangesForward_Bidirection(idx2BWT,c, saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight, resultSaIndexesLeft,resultSaIndexesRight,rev_resultSaIndexesLeft,rev_resultSaIndexesRight)
    Incremental Bi-directional BWT forware search for all SA ranges of Pc where P is a pattern having SA range (saIndexLeft,saIndexRight,rev_saIndexLeft,rev_saIndexRight) for all character c in alphabet set. Position reporting
    
    Position reporting
    ------------------
    > BWTRetrievePositionFromSAIndex(idx2BWT,saIndex,sequenceId,offset)
    Function to output an occurrence from an index within a SA range. The occurring position pointed by an SA index will be output and represented by a sequenceId:offset format which means the position is on the sequenceId-th sequence in the original FASTA reference sequence starting at the offest-th character on the sequence.

Sample Code that uses 2BWT package
----------------------------------

    Sample Program
    --------------
    
    Developer can find sample program that contains example in the 2bwt-lib package. Please see 2bwt-lib-v1.0.0-readme.txt for detail.
    BGI: SOAP2 Package
    
    The short read alignment tools developed by BGI, Short Oligonucleotide Analysis Package, version SOAP2 and SOAP3 are based on 2BWT.

Reference
---------
The 2BWT index and search algorithms used by SOAP2/SOAP3/SOAP3-CPU were developed by the algorithms reserach group of the University of Hong Kong (T.W. Lam, C.M. Liu Alan Tam, Simon Wong, Thomas Wong, Edward Wu and S.M. Yiu).

