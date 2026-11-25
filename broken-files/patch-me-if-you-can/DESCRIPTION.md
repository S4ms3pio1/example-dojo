## PNG

### What is PNG?

- PNG (Portable Network Graphics) is a lossless image format: the pixels you save are the pixels you get back.
- It is common for screenshots, logos, and images with transparency.
- A PNG is very consistent: the start and the end are always the same bytes, and every piece carries a checksum.

### Structure

- A PNG is an 8-byte **signature** followed by **chunks**.
- The chunks go in this order: `IHDR` (the header, always first), `IDAT` (the pixels), and `IEND` (always last). Optional chunks can sit in between.
- Every chunk has four parts, and all numbers are big-endian: **length** (4 bytes), **type** (4 letters, like `IHDR`), **data**, and a **CRC** (4 bytes).
- The CRC is a CRC-32 over the type and the data. The length is not included.

### What Never Changes

- **The first 16 bytes:** `89 50 4E 47 0D 0A 1A 0A` (the signature), `00 00 00 0D` (the `IHDR` length), `49 48 44 52` (`IHDR`).
- **The last 12 bytes:** `00 00 00 00` (length 0), `49 45 4E 44` (`IEND`), `AE 42 60 82` (its CRC).
- So a damaged signature or a missing ending can be rebuilt exactly.

### The IHDR Chunk

- **Bytes `16 – 19`:** width
- **Bytes `20 – 23`:** height
- **Bytes `24 – 28`:** bit depth, color type, compression, filter, interlace
- **Bytes `29 – 32`:** the CRC of bytes `12 – 28`
- Width comes first, then height. A size of zero is not valid.

### How to Identify a PNG

- `file img.png` and `identify img.png` tell you the type and size. If the signature is damaged, `file` says `data`.
- `xxd img.png | head -3` and `xxd img.png | tail -2` show the start and the end.
- If the start is damaged, look for `IHDR` at offset `12` and `IEND` at the end.

### What Can Be Broken, and How to Fix It

- **Signature damaged:** write the 8 fixed bytes back at offset `0`.
- **Bytes `8 – 15` damaged:** write back `00 00 00 0D 49 48 44 52`.
- **Width or height wrong, CRC left as it was:** the stored CRC is the answer key. Try sizes until the CRC of bytes `12 – 28` matches it.
- **IHDR CRC wrong:** recalculate it over bytes `12 – 28` and write it back.
- **Ending missing:** add the last 12 bytes back.
- **Pixel data (`IDAT`) damaged:** the damaged scan data cannot be rebuilt from headers alone.


