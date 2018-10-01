# UCSF NMR data format

## Sparky Manual Descripton

From [Sparky Manual](http://www.cgl.ucsf.edu/home/sparky/manual/files.html#UCSFFormat):

Here is a sketchy description of the UCSF NMR data format as used by Sparky. Sparky needs to know the dimension, data matrix size, data values, nucleus types, and parameters to convert data indices to ppm and Hz. The ppm and Hz scales are determined by knowing along each axis the transmitter frequency, spectral width, and ppm value for index 0. Given this information you can write a UCSF NMR data file for input to Sparky. The file format is somewhat complicated. The data matrix is broken down into small arrays so that small regions of the data can be read with few disk accesses.

A UCSF NMR data file is a binary file with a 180 byte header, followed by 128 byte headers for each axis of the spectrum, followed by the spectrum data. All integer and float values in the file have big endian byte order independent of the native byte order for the machine that wrote the file. An int has big endian order if the most significant byte appears first in the file (or at the lowest memory address). Sun Sparc and SGI Rx000 processors are big endian and DEC Alpha and Intel x86 are little endian. If you write a conversion program to produce UCSF format the code should check the native byte order at run time and reverse floats and ints if necessary before writing them out. All float values are in IEEE format. All header bytes not described below should be set to zero.

| position | bytes | contents | required value |
| ----- | ----- | ------- | ------ |
| 0-9 | 10 | file type | = UCSF NMR (8 character null terminated string) |
| 10 | 1 | dimension of spectrum       | | 
| 11 | 1 | number of data components | = 1 (for real data) |
| 13 | 1 | format version number | = 2 (for current format) |  
