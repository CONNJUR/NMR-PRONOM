# NMRPipe

| Key   | Value  |
| --- | --- |
| Format Name   | NMRPipe  |
| Version   | unknown |
| Description   | NMRPipe is a suite of tools for NMR spectral reconstruction. |
| Extensions    | `.dat`, `.pipe`, `.ft2`, `.ft3`  (No strong conventions)  |
| Mime Types    | None |
| Official Documentation    | https://www.ibbr.umd.edu/nmrpipe/install.html |
| Identification Signature    | The first 4 bytes should be the float 0 (Hex: 00000000).  The second 4 bytes should be either .  The third 4 bytes should be approximately the float 2.345 (LE-Hex: 4016147b  BE-Hex: 7b141640) ( <BR> This can be verified with the bash command `xxd -ps -l 12 filename`.  |
| Representative Files    | [ Example Files ](example-files) |
| Structure | Binary, with metadata embedded in a header that can be rendered human-readable by specialized software tools |
| Primary field of use | Nuclear Magnetic Resonance (NMR) Spectrsoscopy, particularly spectral reconstruction |

## Important Points

* NMRPipe files contain a 2048 byte header.  (An array of 512 floats)
* NMRPipe files can be either big or little endian.
  * The endianness can be determined from the MAGIC sequence - the 3rd float in the header.
* Representative files only contain the first 4096 bytes.
