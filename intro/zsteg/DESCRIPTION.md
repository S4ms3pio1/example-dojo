## zsteg

`zsteg` is an open-source command-line tool written in Ruby, used to detect
and extract data hidden inside PNG and BMP image files via steganography.

The difference between `zsteg` and tools like `strings`/`exiftool` is what
*kind* of hidden data each one looks for: `strings` and `exiftool` extract
embedded text and metadata that already exists as readable bytes in the
file. `zsteg` extracts data hidden inside the **pixel values themselves**,
one bit at a time, data that never appears as readable text anywhere in the
file at all.

To understand how: suppose we have a secret message `HI`, converted to
binary: `H = 01001000`, `I = 01001001`. An image's pixels are raw bytes,
and each pixel is conceptually 3 bytes, one each for Red, Green, and Blue.
The hiding technique loops over each color byte and overwrites its **least
significant bit (LSB)** with the next bit of the secret message, one bit
per byte, a change small enough that it's invisible to the human eye.

Example:
```bash
zsteg -a image.png

imagedata           .. text: "something here"
b1,rgb,lsb,xy       .. text: "exiftool{THERE_IS_NO_SPOON}"
```
`-a` tells `zsteg` to try every bit depth / channel / pixel-order
combination instead of just the most common default, since the hiding
method can vary.


Find the password hidden in the pixel data of `/challenge/image.png`.

The password is wrapped in `zsteg{}`.

Once you find it, run `/challenge/check` and enter the password to get the
flag.

### Going Further

`zsteg` supports many more bit-depth, channel, and ordering combinations
than the default scan, You can read more about its full option set and source
on the [official zsteg GitHub repository](https://github.com/zed-0xff/zsteg).
