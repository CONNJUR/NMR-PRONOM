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

The first byte in the file is position 0. A complex spectrum has two components. Sparky only reads real data and I will only describe below the layout for real data so set the number of components to 1. Use format version number 2. 

For each axis of the spectrum write a 128 byte header of the following form:

| position | bytes | contents |
| ----- | ----- | ------- |   
| 0 | 6 | nucleus name (1H, 13C, 15N, 31P, ...) null terminated ASCII  |
| 8 | 4 | integer number of data points along this axis    |            
| 16 | 4  | integer tile size along this axis     |                       
| 20 | 4	  | float spectrometer frequency for this nucleus (MHz)        |  
| 24 | 4 | float spectral width (Hz)       |                             
| 28 | 4 | float center of data (ppm)     |  

Next comes the spectrum data floating point values. The full data matrix is divided into tiles. Each tile is written to the file as an array with the highest axis varies fastest. If a tile size does not evenly divide the data matrix size then there will be a partial tiles at the edge. Partial tiles are written out as a full tiles with zero values for the region outside the data matrix. The tiles themselves form an array and are written out in sequence with highest axis varying fastest.

Here is a detailed example of how the floating point values are written. Suppose we have a 2D spectrum with matrix that is 1024 by 4096 data points. Each value is a 4 byte IEEE floating point number. So the data takes up 16 MBytes (1024 x 4096 x 4 bytes). Sparky wants to be able to show you a small region of the spectrum without any delay, so the data is stored in small rectangular blocks (tiles). Only the tiles needed to display the region you are viewing need to be read from disk. The size of tiles in UCSF format files is typically 32 Kbytes or 16 Kbytes, although a little bit bigger sizes like 128 Kbytes would be reasonable with current disk drive seek times (circa 1999). The current conversion programs that produce UCSF format divide each axis of the data matrix by 2 in steps until the resulting tile size is less than or equal to 32 Kbytes. For purposes of example I will instead take tiles that are 128 by 256 floating point values. So each one is 128 Kbytes ( = 128 * 256 * 4). Then an 8 = (1024 / 128) by 16 = (4096 / 256) grid of tiles covers the 1024 by 4096 data matrix. Denote a point in the data matrix like (312,1587) and a tile {2,5}. Call the two axes w1 and w2. Tile {0,0} includes a rectangle of data values from 0 to 127 along axis w1, and 0 to 255 along axis w2. Tile {a,b} is the data rectangle a*128 to (a+1)*128 - 1 along axis w1 and b*256 to (b+1)*256 - 1 along axis w2.

You write the tiles in the following order.

{0,0}
{0,1}
{0,2}
.
.
{0,7}

{1,0}
{1,1}
{1,2}
.
.
{1,7}

.
.
.

{15,0}
{15,1}
{15,2}
.
.
{15,7}

'''This order is what was meant above by the phrase "highest axis varies fastest". In this case the w2 axis varies fastest.''' Each tile is output with the same rule. Tile {0,0} has its 128 by 256 floating point values output in the following order:

(0,0)
(0,1)
(0,2)
.
.
(0,255)

(1,0)
(1,1)
(1,2)
.
.
(1,255)

.
.
.

(127,0)
(127,1)
(127,2)
.
.
(127,255)

For 3D data the tiles are 3D data cubes. The order of writing the grid of tiles out has the w3 axis varying fastest, w2 second fastest, and w1 slowest. Likewise each tile's cube of floating point values are written out in order having the w3 axis vary fastest, w2 second fastest, and w1 slowest.

## Sparky Source Code

This is a more detailed description of the binary header information.  This description stems from an examination of the Sparky Source Code, in particular, the file ucsffile.cc.  The 180 byte header is essentially the NMR_HEADER data structure, while the 128 byte headers are the NMR_AXIS data structure.  Each structure has unused pointers.  Unused is saved in the axis header, the other is not saved to the file header.  However, the file header does include byte padding for alignment of the structure fields by a 32-bit compiler.  (Key: Green refers to metadata which is typically set correctly by Sparky and useful to manage.  Yellow are fields which are either not typically set or are not used by anything.)

The 180 byte header contains:

| position | bytes | field name | description | purpose / options |
| ----- | ----- | ------- | ----- | ------- |   
|0-9	  | 10	 | ident  | file type	 | "UCSF NMR" required for version 2 (null terminated ASCII) |
|10	   | 1	| naxis  | number of dimensions  |  Must be 2, 3 or 4       |
|11	  | 1	| ncomponents  | number of data components |  Must be 1 (real data, not complex or hypercomplex) |
|12	  | 1	| encoding   | unknown   |  Seems to be always set to false: 0    |
|13	 | 1	| version  | format version number |  Must be 2 (for current format)    |
|14-22	 | 9	| owner   | user name who created file  |  whoever executed pipe2ucsf (null terminated ASCII) |
|23-48	  | 26	| date   | date user created file   | when pipe2ucsf was executed (null terminated ASCII) |
|49-128	   | 80	| comment  | unknown    |  Does not appear to be used (null terminated ASCII) |
|129-131   | 3	| ----   | byte padding   |  structure alignment by compiler       |
|132-139   | 8	| seek_pos   | type long  | Does not appear to be used   |
|140-179 | 40	| scratch   | unknown   |  Does not appear to be used (null terminated ASCII) |

For each axis of the spectrum there is a 128 byte header of the following form:

|position | bytes | bit | data type | field name | contents  |
| ----- | ----- | ------- | ----- | ------- | ------ |
|0-5 | 6	 |  | char[] | nucleus 		 | nucleus name (1H, 13C, 15N, 31P, ...) null terminated ASCII |
|6-7 | 2	 |  | short  | spectral_shift  | spectral shift: seems to be left or right circular shift? |
|8-11 | 4	  | 	 | unsigned int | npoints 	 | number of active data points along this axis  |
|12-15 | 4	 | 	 | unsigned int  | size 	 | total size of axis  |
|16-19 | 4   | 	 | unsigned int | bsize 	 | integer tile size (# of points per cache block) |
|20-23 | 4	 | 	 | float   | spectrometer_freq 	 | spectrometer frequency for this nucleus (MHz) |
|24-27 | 4	 | 	 | float  | spectral_width 	 | spectral width (Hz)  |
|28-31 | 4	 | 	 | float  | xmtr_freq 	 | center of data (ppm) |
|32-35 | 4	 | 	 | float  | zero_order 	 | phase correction  (unknown units)  |
|35-39 | 4	 | 	 | float   | first_order 		 | phase correction  (unknown units)  |
|40-43 | 4	 | 	 | float  | first_pt_scale	 | scaling for first point  |
