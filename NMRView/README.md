# NMRView

| Key   | Value  |
| --- | --- |
| Format Name   | NMRView  |
| Version   | ? |
| Description   | NMRView is a graphical NMR assignment and integration program for proteins, nucleic acids, and other polymers. |
| Extensions    | `.nv` |
| Mime Types    | None |
| Official Documentation    | http://docs.nmrfx.org/processor/files/fformat |
| Identification Signature    | The first 4 bytes should be the integer 874032077.  If the Endianness is reversed, it will be the number -844425164. |
| Representative Files    | [ Example Files ](example-files) |
| Structure | Binary, with metadata embedded in a header that can be rendered human-readable by specialized software tools |
| Primary field of use | Nuclear Magnetic Resonance (NMR) Spectrsoscopy, particularly spectral analysis |

## Important Points

* NMRview files contain a 2048 byte header.
* NMRView files can be either big or little endian.
  * The endianness can be determined from the MAGIC sequence.
* Representative files only contain the first 4096 bytes.
