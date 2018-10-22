# NMRPipe File Format

Mostly from fdatap.h 

The NMRPipe header is 2048 bytes. It is essentially a 512 element array of 4-byte floats. However, some of the contiguous floats are actually recast into char's for storing strings. Integer metadata is simply recorded as floats. fdatap.h defines named constants to provide a key-value lookup using the array indices.  

| Array Index | Name | Comment |
| ------- | ----  | ------ |
|0	|FDMAGIC		|Should be zero in valid NMRPipe file
|1		|FDFLTFORMAT		|Constant defining floating point format. (IEEE or VAX).
|2		|FDFLTORDER		|Constant for determining endieness.  2.345 is correct.
|3-8		|unused		|

