## ZIP

### What is a ZIP?

* **ZIP** is an archive format that bundles files together using lossless compression (usually **DEFLATE**) with a directory index at the end.
* Unlike images that read from top to bottom, ZIP tools read **backwards**—finding the trailer at the end first to locate the index, then jumping to each file.
* All multi-byte numbers inside a ZIP are **little-endian**.

### Structure

A standard ZIP file has three main sections:

1. **Local File Header (`50 4B 03 04`)** — Sits right before each file's compressed data.
2. **Central Directory (`50 4B 01 02`)** — The master index near the end of the file listing every entry and its sizes.
3. **End of Central Directory (`50 4B 05 06`)** — The 22-byte trailer at the very end that points to where the Central Directory starts.

### Key Offsets

#### Local File Header (starts at `0x00`)
* **Bytes `0 – 3`:** Signature (`50 4B 03 04` / `PK\x03\x04`)
* **Bytes `6 – 7`:** Bit flag (`00 00` = normal, bit `0` set = encrypted)
* **Bytes `18 – 21` (`0x12`):** Compressed size
* **Bytes `22 – 25` (`0x16`):** Uncompressed size
* **Bytes `26 – 29` (`0x1A`):** Filename length (`n`) and extra field length (`m`)

#### Central Directory (`50 4B 01 02`)
* **`+0`:** Signature (`50 4B 01 02` / `PK\x01\x02`)
* **`+8`:** Bit flag (mirrors the Local File Header flag)
* **`+20` (`+0x14`):** Compressed size (backup copy of the local size)

#### End of Central Directory (`50 4B 05 06`)
* **`+0`:** Signature (`50 4B 05 06` / `PK\x05\x06`)
* **`+16` (`+0x10`):** Byte offset where the Central Directory (`50 4B 01 02`) starts
* **`+20` (`+0x14`):** Comment length (`00 00` if empty)

