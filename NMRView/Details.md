# NMRView File Format

From [NMRView Documentation](http://docs.nmrfx.org/processor/files/fformat):

NMRViewJ/NMRFx files consist of a header at the beginning of the file followed by the actual data values in a sub-matrix format. The header describes the layout of the file and reference information. The file can be tested to see if is an NMRview/NMRFx file by reading the first 4 bytes.
This should represent an integer with value equal to '''874032077'''.
Files are normally stored with integer and floating point values in big-endian, but can be in little-endian format. The endianness can be tested by checking which mode gives the magic value as 874032077.

The first section of the header (typically first 1024 bytes) stores information about the entire file.


|Position	|Bytes	|Type	|Name			|Comment     |
| ------- | ---- | ------- | -------- | ------ |
|0-3		|4	|int	|magic			|Should be equal to 874032077 if this is an NMRView file.|
|4-7		|4	|int	|version		|Currently set to 0|
|8-11		|4	|int	|unused		|Unused, but may be used in subsequent versions.  |
|12-15		|4	|int	|fileHeaderSize	|The size of the header at beginning of file. Typically 2048 bytes for NMRViewJ |
|16-19		|4	|int	|blockHeaderSize	|The size of the header at the beginning of each data block (sub-matrix tile). Typically 0.|
|20-23		|4	|int	|blockElements		|The number of elements in each sub-matrix|
|24-27		|4	|int	|nDim			|The number of dimensions in the file |
|28-1023	|996	|byte	|unused		|Unused, but may be used in subsequent versions.|

The second section of the header (typically second 1024 bytes) stores information about each dimension. Each dimension uses 128 bytes, allowing for 8 dimensions in the current version.

The actual position of each dimension section is calculated by adding the Position value in the table below to 1024 + dim * 128 (where dim = 0,1,...)

|Position	|Bytes	|Type	|Name			|Comment    | 
| ------- | -------- | -------- | ------- | ------- |
|0-3		|4	|int	|size			|The number of data points along this dimension|
|4-7		|4	|int	|blockSize		|The number of data points in a block, along this dimension|
|8-11		|4	|int	|nBlocks		|The total number of blocks in the file, the value is ignored on reading and calculated from the above sizes|
|12-15		|4	|int	|unused		|Unused, but reserved for future use.  |
|16-19		|4	|int	|unused		|Unused, but reserved for future use.|
|20-23		|4	|int	|unused		|Unused, but reserved for future use.  |
|24-27		|4	|float	|sf			|The spectrometer frequency for this dimension (Mhz)|
|28-31		|4	|float	|sw			|The sweep width for this dimension (Hz)|
|32-35		|4	|float	|refpt			|The data point at which the reference is specified. Typically the center of spectrum.|
|36-39		|4	|float	|refval		|The reference value at the above reference point.|
|40-43		|4	|int	|refunits		|The units the reference is specified in, typically the integer 3, indicating ppm.|
|44-47		|4	|float	|foldUp		|Unused at present|
|48-51		|4	|float	|foldDown		|Unused at present|
|52-67		|16	|char	|label			|The label for this dimension of the axis. If less than 16 characters it should be terminated with a null byte (0)|
|68-71		|4	|int	|complex		|0 if the data along this dimension is real, 1 if it is complex|
|72-75		|4	|int	|freqdomain		|0 if the data along this dimension is in the time domain, 1 if it is in the frequency domain|
|76-79		|4	|float	|ph0			|The cumulateive zero-order phase correction applied along this dimension.|
|80-83		|4	|float	|ph1			|The cumulateive first-order phase correction applied along this dimension.|
|84-87		|4	|int	|vsize			|The number of data points along this dimension that have valid data. Used during processing.|
|88-127		|40	|byte	|unused		|Unused, but reserved for future use.|

The actual data values are stored in a sub-matrix (bricked or tiled) format. Using this format allows more rapid and efficient access to data as it minimizes the total amount of data that needs to be read when a subset of the data is read or when data is accessed along the second or higher dimension.

Values, in the current version, are stored as 4-byte floating point numbers.

An example of the data storage should be helpful in understanding the layout. A 2D file with sub-matrix block size of 4 on each dimension would be arranged so that reading the values sequentially from the file would give values corresponding to the positons listed below. The value 1,0 corresponds, for example, to a point at the second position of the first dimension and the first position of the second dimension (counting from 0). The first dimension corresponds to the data described by the first dimension section of the header.


 0,0  block 0
 1,0
 2,0
 3,0
 0,1
 1,1
 2,1
 3,1
 ...
 0,3
 1,3
 2,3
 3,3 

 4,0  block 1
 5,0
 6,0
 7,0
 4,1
 5,1
 6,1
 7,1
 ...
 4,3
 5,3
 6,3
 7,3
 ...
