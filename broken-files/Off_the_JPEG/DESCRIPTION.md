## JPEG — Height and Width

### 1. Find the Fields

Walk the segments to the SOF (`FF C0` baseline or `FF C2` progressive).
You can see them using `hexedit`: open `hexedit img.jpg`, press `/`, and you will see `Hexa string to search:`. Type `ff c2` (or `ff c0`) and press `Enter`.

```text
ff c2  00 11  08  [HH HH] [WW WW]  03 ...

HH HH: Height (comes first), big-endian
WW WW: Width (comes second), big-endian
```

Example: `02 8f 03 e8` = height `0x028F` = 655, width `0x03E8` = 1000.

Check the bytes around your match: a real SOF is followed by `00 11 08`. The same two bytes can also appear inside a thumbnail or the image data, so skip any match that doesn't fit.

### 2. What the Damage Looks Like

- **Empty JPEG image (DNL not supported):** a dimension is `00 00`, the decoder stops, no picture
- **Picture leans or shears diagonally:** width wrong by a small amount
- **Picture scrambled into bands:** width wrong by a lot
- **Only the top of the picture, the rest missing:** height too small
- **Full picture, then a flat grey block at the bottom:** height too big

*(Exact looks can differ slightly between viewers.)*

### 3. Method A: Thumbnail (Exact, if There Is One)

An EXIF thumbnail is a smaller copy of the same picture, so it has the same aspect ratio.
Thumbnails are optional (cameras and phones add them, most generated or stripped images don't), so check first:

```bash
_> exiftool -ThumbnailImage file.jpg
_> exiftool -b -ThumbnailImage file.jpg > thumb.jpg
_> identify thumb.jpg
```

Then:

```text
broken width :  width  = height * thumb_w / thumb_h
broken height:  height = width  * thumb_h / thumb_w
```

Never use the broken field in the formula.
Example: thumbnail `160x100`, intact height `500`, so `width = 500 x 1.6 = 800 = 03 20`.
A stale thumbnail (image edited afterwards) gives a wrong ratio.

### 4. Method B: Fix It by Hand, or Brute Force It

No thumbnail? A broken size can still be fixed.

1. **Check what is corrupted.** Inspect the SOF fields.
2. **Reason about it.** Which field is wrong, the height or the width? Look at both values and ask which one is impossible or doesn't fit the picture.
   Example: `00 00 04 00` is height = `0` and width = `1024`. A height of `0` can't be right, while `1024` is a reasonable width, so the height is the broken field.
3. **Patch it manually.** Change the value in `hexedit` (on a copy), save, open the image, and check whether it looks right. Adjust and repeat until it does.
4. **Or brute force it with a Python script.** Try values for the broken field, write each one into the SOF, and check the result.

You don't need the exact original size: a value a few pixels off still shows the whole picture.

If **both** height and width are broken, several combinations can look almost right (a swapped height and width is a common one), so compare the candidates by eye.

### 5. Bottom Line

Height and width can be restored as long as the compressed picture data itself is intact, because only the header numbers are wrong. A JPEG with a damaged scan can't be fully recovered.

