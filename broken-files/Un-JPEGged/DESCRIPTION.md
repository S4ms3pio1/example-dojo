## JPEG

### What is JPEG?

JPEG (Joint Photographic Experts Group) is a lossy image format commonly used for photographs. It reduces file size by discarding image details that are less noticeable to the human eye.

### JPEG Structure

#### 1. Start of Image (Magic Number)

Every JPEG starts with the following two bytes:

```text
FF D8
```

This is called the SOI (Start of Image) marker. It identifies the beginning of a JPEG file.
The byte after it is always `FF` again (the start of the first segment), so the full signature you will see is `FF D8 FF`.

#### 2. JPEG Header

After the starting marker, a JPEG contains several segments holding additional information.

A segment generally has this structure:

```text
[MARKER] [LENGTH] [DATA]
```

For example:

```text
FF D8  FF FE  00 10  4C 61 76 63 ...
│      │      │      └── Comment data
│      │      └───────── Length
│      └──────────────── Comment marker
└─────────────────────── Start of Image
```

The length is stored in two bytes (big-endian). It includes the length bytes themselves, but not the marker.
So `00 10` = 16 bytes: the 2 length bytes plus 14 bytes of data.

`FF FE` identifies a COM (Comment) segment. Its contents can be plain text. In these files, the comment contains:

```text
Lavc60.31.102
```

Libavcodec is FFmpeg's codec library (it is used for encoding as well as decoding).
FFmpeg stamps its name into the JPEGs it writes, so this comment tells you the file was made with FFmpeg (libavcodec version 60.31.102).

#### 3. Common JPEG Markers

Some markers you may encounter:

```text
FF D8  - Start of Image (SOI)
FF E0  - JFIF label (APP0)
FF E1  - Exif data (APP1)
FF FE  - Comment (COM)
FF DB  - Quantization Table (DQT)
FF C0  - Start of Frame, baseline (SOF0)   <- image height and width
FF C2  - Start of Frame, progressive (SOF2) <- image height and width
FF C4  - Huffman Table (DHT)
FF DA  - Start of Scan (SOS)
FF D9  - End of Image (EOI)
```

The Start of Frame (SOF) segment holds the image's **height and width**, so it is the segment to look at when a picture opens wrong or not at all.

#### 4. Image Data

After the JPEG header comes the actual compressed image data.

The `FF DA` marker (Start of Scan) indicates where the compressed image data begins.

#### 5. End of Image

A JPEG normally ends with:

```text
FF D9
```

### How to Identify a JPEG

#### Tools

```bash
_> file img.jpg
_> identify img.jpg
img.jpg JPEG 3890x5835 3890x5835+0+0 8-bit sRGB 3.82761MiB 0.000u 0:00.000
```

- `file` identifies the format using file signatures.
- `identify` decodes the image and reports its dimensions and other properties.

If the JPEG signature is damaged, `file` reports `data`, while `identify` throws an error like:
`Not a JPEG file: starts with 0xXX 0xXX 'file_name' @ error/jpeg.c/JPEGErrorHandler/339.`
That error also tells you the first bytes it saw.

Then use `xxd` to inspect the file manually.

#### Hex Clues

Look at the ASCII column of `xxd` near offset 6, right after the first segment's length:
- `JFIF` — APP0 (JFIF header)
- `Exif` — APP1 (camera metadata)
- `Lavc` — COM (an FFmpeg comment, like these files)

These labels are optional. `strings file | head` shows them too.

Repeated `FF xx` markers (`FF DB`, `FF C4`, `FF DA`) mean JPEG segments, and the file ends with `FF D9`.

```text
_> xxd imgExif.jpg | head -n 1
00000000: ffd8 ffe1 0080 4578 6966 0000 4949 2a00  ......Exif..II*.

_> xxd imgJFIF.jpg | head -n 1
00000000: ffd8 ffe0 0010 4a46 4946 0001 0100 0001  ......JFIF......
```

### hexedit

`hexedit` views and edits a file in hexadecimal and ASCII. You can modify the file and search through it.
Work on a copy: `cp file.jpg fixed.jpg`, then `hexedit fixed.jpg`.

The screen has three columns: on the left the offset (byte position in the file), in the middle the hex view, on the right the ASCII view.

#### Essential Keyboard Shortcuts
- `Arrow` keys: Move the cursor
- `Tab`: Switch between the hex column and the ASCII column
- `/`: Search (hex or ASCII, depending on the column you are in). Type your bytes, press `Enter`, and the cursor jumps to the match
- `Ctrl+U`: Undo
- `Ctrl+X`: Save and exit (it asks to confirm)
- `Ctrl+C`: Exit without saving

Learn more with `man hexedit`.

