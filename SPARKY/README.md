# SPARKY

| Key   | Value  |
| --- | --- |
| Format Name   | Sparky  |
| Version   | 2 |
| Description   | Sparky is a graphical NMR assignment and integration program for proteins, nucleic acids, and other polymers. |
| Extensions    | No formal extension; however, it is common to use `.ucsf` |
| Mime Types    | None |
| Official Documentation    | http://www.cgl.ucsf.edu/home/sparky/ <BR> http://www.nmrfam.wisc.edu/nmrfam-sparky-distribution.htm |
| Identification Signature    | The first 9 bytes are the null-terminated string 'UCSF NMR'.  <BR> This can be verified with the bash command `head -c 9 filename` <BR> In addition, the 14th byte should be the number 2 (unsigned char, for version 2). <BR> Other details in Format Details |
| Representative Files    | [ Example Files ](example-files) |

## Important Points

* Sparky files contain a 180-byte main header, followed by 2-4, 128-byte dimension headers.
* Sparky files are always big endian.
  * This does not matter for the digital signature.  The key aspects are the first 9 characters and the 14th.  The first 144 bytes are all single-byte characters.
* Representative files only contain the first 1024 bytes.
* The header portions may differ between 32-bit and 64-bit versions.  This has yet to be examined.  However, first 9 characters and the 13th should be the same.
