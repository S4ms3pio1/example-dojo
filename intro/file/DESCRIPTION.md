## file

`file` reads a file's header bytes to determine what type it actually is,
regardless of its extension.

Example:
```
// magic number of a PNG file
89 50 4e 47 0d 0a 1a 0a

// magic number of a ZIP file
50 4b 03 04

// Plain ASCII text has no required magic number.
```

You can verify this yourself by running:
```
xxd your_ascii_file | head
```
and noticing the text starts immediately, with no magic header showing:
```
00000000: 7468 6973 2069 7320 6173 6369 6920 6669  this is ascii fi
00000010: 6c65 200a                                le .
```

`file` is important because it lets you understand what type of file
you're actually dealing with, independent of its extension, and where to
move on from there.

Check the files in `/challenge` **in sequence**. The password consists of the file types joined with underscores, wrapped in `file{}`.

Once you find it, run `/challenge/check` and enter the password to get the
flag.

Example:
```
file my_file
my_file: MKL, XXX-Z XXX

file my_file2
my_file2: XYC, XXX-Z XXX

The password will look something like this: `file{MKL_XYC}`
```
