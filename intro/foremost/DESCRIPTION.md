## foremost

`foremost` is a file carving tool. It scans raw bytes for known file headers and footers and recovers files without needing a filesystem table, a filename, or an extension.

A filesystem maintains metadata describing files and where their data is stored. When that table is missing (deleted files, formatted disks, raw images) or when a file is hidden inside another, you can't ask "where are the files?" so you scan for signatures instead. For example, a JPEG starts with `FF D8 FF` and ends with `FF D9`.

Appending files to each other is enough to hide them. Many image viewers stop parsing at the end of the image, so additional data appended afterward may go unnoticed:

```bash
_> ls
audio.wav  img_1.jpg  img_2.jpg
_> cat img_1.jpg img_2.jpg audio.wav > final
_> file final
final: JPEG image data, JFIF standard 1.01, resolution (DPI), density 96x96, segment length 16, baseline, precision 8, 1379x919, components 3
```

It still opens as a normal image, and the second image and the WAV sit invisible after it.

`binwalk` Example:

```bash
_> binwalk -e final

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
530503        0x81847         JPEG image data, EXIF standard
530515        0x81853         TIFF image data, little-endian offset of first image directory: 8

_> ls
audio.wav  final  img_1.jpg  img_2.jpg
```

`binwalk` reported a second JPEG at offset 530503 (the TIFF header at 530515 is part of that same image), but `-e` extracted nothing. The `-e` flag automatically extracts only the file types it has extraction rules for, and JPEG isn't one of them. To pull out other formats, we need to tell binwalk what to carve with `--dd`, using `'type:extension'`. 

```bash
_> binwalk --dd="jpg:jpg" final
# same report, nothing extracted

_> binwalk --dd="jpeg:jpg" final
_> ls
audio.wav  final  _final.extracted  img_1.jpg  img_2.jpg
_> ls _final.extracted/
0.jpg  81847.jpg
```

Both images were carved, but binwalk never mentioned the WAV at the end of the file, so it can't be extracted this way. 

`foremost` Example:

```bash
_> foremost -i final -o output
Processing: final
|*|
_> ls output/
audit.txt  jpg  wav
_> cat output/audit.txt
...
Num      Name (bs=512)         Size      File Offset     Comment

0:       00000000.jpg          518 KB              0
1:       00001036.jpg          1 MB           530503
2:       00003131.wav          1 MB          1603404

3 FILES EXTRACTED
jpg:= 2
rif:= 1  # rif is the file type category Foremost uses for RIFF-based files, including WAV.
```

As we can see, foremost found and carved both JPEGs and the WAV in one command, even though `final` is named and typed as a single image. It works from the content, not the name. The audit file lists every file found and its offset.
You can confirm the hidden file yourself without any tool:

```bash
_> xxd -s 1603404 -l 16 final
0018774c: 5249 4646 2434 1100 5741 5645 666d 7420  RIFF$4..WAVEfmt 

```

You should see `RIFF....WAVE` at that offset

You can also carve by hand with `dd`, which is exactly what foremost automates.

Keep in mind that fragmented files can be carved incompletely, formats without a clear footer rely on a max size and may include junk, and a random byte sequence can look like a header (false positive).

Use `binwalk` for an overview, then `foremost` or `dd` to carve, and verify with `xxd`.

Find the hidden file inside the provided image, carve it out, and reveal what's hidden inside.

The password will not be like previous challenges. You will have to find it, combine its parts using underscores, and wrap the final result with foremost{}. 

You don't have to worry about capital letters or order you will figure that out.

Once you find it, run `/challenge/check` and enter the password to get the flag.
