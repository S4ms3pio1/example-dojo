## GIF Me A Break

### What is GIF?

* GIF (Graphics Interchange Format) is a binary image format built for small animations and indexed color graphics.
* Every GIF89a file follows a strict byte-level layout: a **Header**, a **Logical Screen Descriptor**, an optional **Global Color Table (palette)**, **Image Descriptors**, and **LZW-compressed pixel data**.
* Unlike modern formats that store raw RGB values per pixel, GIF stores a color palette up front and compresses pixel indices using **LZW (Lempel-Ziv-Welch)** compression.
* Every file ends with a mandatory single-byte **Trailer** (`0x3b`).

### GIF89a Byte Layout

* **Header (Bytes `0 – 5`):** Always `47 49 46 38 39 61` (`GIF89a`) or `47 49 46 38 37 61` (`GIF87a`).
* **Logical Screen Descriptor (Bytes `6 – 12`):**
  * Bytes `6 – 7`: Canvas width (little-endian)
  * Bytes `8 – 9`: Canvas height (little-endian)
  * **Byte `10` (`0x0A`) — Packed Fields:** Bit 7 (the most significant bit, `0x80`) is the **Global Color Table Flag** (`1` = palette present, `0` = no palette). Bits `0 – 2` store the palette size exponent `N`, where the palette has `2^(N + 1)` colors (`3 * 2^(N + 1)` bytes).
  * Byte `11`: Background color index
  * Byte `12`: Pixel aspect ratio
* **Global Color Table (Starts at Byte `13` / `0x0D`):** If Bit 7 of Byte `10` is `1`, raw RGB triplets follow immediately (e.g., `N = 7` means `256` colors × `3` bytes = `768` bytes, spanning `0x0D` to `0x30C`).
* **Blocks & Image Descriptor:**
  * Extension blocks start with `0x21` (such as `21 F9` for Graphic Control or `21 FF` for Application Extensions) and end with a `0x00` block terminator.
  * Each frame begins with an **Image Descriptor** starting with `0x2C` (`10` bytes total: `2C`, 2-byte left, 2-byte top, 2-byte width, 2-byte height, and 1-byte local packed field).
  * Immediately after the 10-byte Image Descriptor (at `2C` offset `+10`) sits the 1-byte **LZW Minimum Code Size** (which matches the color bit depth, e.g., `0x08` for an 8-bit / 256-color palette, and cannot be `0x00`).
* **Trailer (Last Byte):** Always `0x3b` (`;`).

### What to Look At

* These files are structurally broken and will not open in standard image viewers.
* No pixel data was deleted, and no external backups are needed—every broken byte can be determined from the surrounding file structure and the layout above.
* Check the **magic bytes and trailer**, the **Logical Screen Descriptor flags**, and the **LZW Minimum Code Size** before each image stream.

> **Note:** This challenge is 100% solvable manually using `hexedit` alone, though writing a quick Python script to parse and patch the binary structure is great practice for larger forensics tasks.
