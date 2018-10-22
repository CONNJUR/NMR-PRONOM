# NMRPipe File Format

Mostly from fdatap.h 

The NMRPipe header is 2048 bytes. It is essentially a 512 element array of 4-byte floats. However, some of the contiguous floats are actually recast into char's for storing strings. Integer metadata is simply recorded as floats. fdatap.h defines named constants to provide a key-value lookup using the array indices. 

Two notes. One, the actual data sizes of the file appear to be given by array elements 99, 219, 15 and 32, corresponding to FDSIZE, FDSPECNUM, FDF3SIZE and FDF4SIZE. Two, the "correct first point" C1 value and the ZF sizes appear to be negated. (Array elements: 108, 404, 409, 418, 423, 437, 438 and 439) 


| Array Index | Name | Comment |
| ------- | ----  | ------ |

