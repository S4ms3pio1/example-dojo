## exiftool

`exiftool` allows you to read, write, and edit metadata in a wide variety of files.

Example:
```bash
# Basic metadata extraction
_> exiftool image.jpeg

ExifTool Version Number         : 12.76
File Name                       : image.jpeg
Directory                       : .
File Size                       : 146 kB
...
File Permissions                : -rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg


# More detailed metadata inspection
_> exiftool -a -u -g1 -s image.jpeg

---- ExifTool ----
ExifToolVersion                 : 12.76
---- System ----
FileSize                        : 710 kB
FilePermissions                 : -rw-rw-r--
---- File ----
FileType                        : JPEG
...
---- PNG ----
...
---- Composite ----
...

```

Find the password in the metadata of `/challenge/image`.

The password is wrapped in `exiftool{}`. Read the description in the metadata to understand how the password was generated.

Once you find it, run `/challenge/check` and enter the password to get the flag.

### Going Further

ExifTool is a lot more powerful than just reading basic details! You can read more about its extensive features, supported formats, and documentation on the [official ExifTool website](https://exiftool.org/).


