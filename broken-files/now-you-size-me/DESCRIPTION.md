## Recovering Missing PNG Width & Height Using CRC32

### The Idea
* Every PNG header stores a **CRC32**: a 4-byte fingerprint of the header chunk.
* Even when the width and height are zeroed out, the original CRC32 is still intact.
* You can brute-force width and height pairs until `crc32(IHDR + width + height + settings)` matches the stored CRC32.

### Byte Map (0-Indexed)
* **Bytes `0 – 7`:** PNG Signature (`89 50 4E 47 0D 0A 1A 0A`)
* **Bytes `8 – 11`:** IHDR Length (`00 00 00 0D`) — *Not included in CRC*
* **Bytes `12 – 15`:** Chunk Name (`IHDR`) — **CRC starts at byte `12` (`0x0C`)**
* **Bytes `16 – 19`:** Width (4 bytes, big-endian, zeroed)
* **Bytes `20 – 23`:** Height (4 bytes, big-endian, zeroed)
* **Bytes `24 – 28`:** Bit depth, color type, compression, filter, interlace — **CRC ends at byte `28` (`0x1C`)**
* **Bytes `29 – 32`:** Stored CRC32 checksum — **Starts at byte `29` (`0x1D`)**

*(In Python, slicing `data[12:29]` selects bytes `12` through `28`.)*

### How to Fix It
1. Inspect the first 33 bytes.
2. Read the target CRC32 at bytes `29 – 32` and keep the 5 setting bytes at `24 – 28`.
3. Loop through possible `(width, height)` values in Python using `struct.pack(">II", w, h)` and `zlib.crc32()`.
4. Once the CRC matches, fix the corrupted dimension fields in the header.

> **Note:** You might want to use Python with the `zlib` and `struct` libraries to solve this one.
