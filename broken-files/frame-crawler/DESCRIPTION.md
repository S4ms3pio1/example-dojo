# Frame_Crawler

> *"A friend is a gift you give yourself."*

## 1. What is an MP4 File?
An **MP4 (MPEG-4 Part 14)** is not a raw stream of pixels—it is a binary multimedia container built on the **ISO Base Media File Format**. It wraps compressed video and audio streams alongside timing, resolution, and indexing metadata so media players know how to decode and seek through the footage.

---

## 2. File Structure (Boxes / Atoms)
An MP4 file is organized into sequential binary chunks called **Boxes** (historically called **Atoms**):

1. **File Type Box (`ftyp`):** Always sits at the very beginning of the file (`offset 0x00`). It acts as the file signature, declaring the MP4 brand and compatibility versions so tools recognize the container.
2. **Movie Box (`moov`):** The master metadata container. It holds the timeline, frame rate, video dimensions, and lookup tables telling the player where every frame lives.
3. **Media Data Box (`mdat`):** The largest chunk in the file—it holds the actual raw, compressed video and audio payload.

---

## 3. What is an Atom / Box?
Every MP4 box follows a strict **8-byte header** followed by its payload data:

```text
[ 4-byte Size (Big-Endian) ] [ 4-byte Type (ASCII) ] [ Payload Data ... ]
```

* **Size:** A 32-bit big-endian integer telling the parser how many bytes long the entire box is (including the 8-byte header).
* **Type:** A 4-character **ASCII** identifier (such as `ftyp`, `moov`, `trak`, `mdia`, `minf`, `stbl`, or `mdat`).

> **Hex Editor Note:** Box sizes are stored as raw big-endian binary integers, while box types are stored as plain **4-byte ASCII text** (for example, `'f'` is `0x66`, `'t'` is `0x74`). If an ASCII type tag is zeroed out or altered, players lose track of the container hierarchy.

---

## 4. How Boxes Connect
MP4 boxes form a **nested hierarchy**—parent boxes contain child boxes inside their byte boundaries:
* The top-level **`moov`** box wraps one or more **`trak`** (track) boxes—one for video, one for audio.
* Inside each **`trak`**, nested boxes (`mdia` → `minf` → `stbl`) store the sample tables (`stco`, `stsz`) that point directly to byte offsets inside **`mdat`**.

If the top-level signature or core container tags are damaged, decoders cannot traverse the tree to unpack the video frames.

---

