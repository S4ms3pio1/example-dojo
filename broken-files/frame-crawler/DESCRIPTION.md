## MP4


### Structure (Boxes / Atoms)
An **MP4** is built out of nested chunks called **Boxes** (or **Atoms**). Every box starts with an **8-byte header**:

```text
[ 4-byte Size (Big-Endian) ] [ 4-byte Type (ASCII) ] [ Payload / Child Boxes ... ]
```

* **Size:** Total length of the box in bytes (including the 8-byte header). Jumping forward by `Size` bytes lands you directly on the next sibling box.
* **Type:** A 4-character **ASCII** tag (`ftyp` = `66 74 79 70`).

### Core Box Hierarchy
1. **`ftyp` (File Type):** Always the first box at `0x00` (its ASCII tag sits at `0x04`).
2. **`moov` (Movie Container):** Holds all video metadata and frame lookup tables.
   * **`trak` (Track):** Sits inside `moov` and defines the video/audio stream.
3. **`mdat` (Media Data):** Holds the raw compressed video frames.

