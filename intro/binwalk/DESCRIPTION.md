## binwalk

`binwalk` is a tool for searching binary images for embedded files and executable code, It can also identify and optionally extract them.


After a ZIP file is embedded into a file, strings can sometimes reveal its content if it contains printable ASCII characters:

```bash
_> echo "fake_password{inside_a_zip_file}" > flag.txt
_> zip secret.zip flag.txt
_> cat image.png secret.zip > image_cp.png
_> strings image_cp.png | grep fake
fake_password{inside_a_zip_file}
```

`binwalk` Example:
```bash
_> binwalk challenge.png 
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 500 x 300, 8-bit/color RGB, non-interlaced
41            0x29            Zlib compressed data, default compression
1142          0x476           Zip archive data, at least v2.0 to extract, compressed size: 78, uncompressed size: 78, name: message.txt
1318          0x526           End of Zip archive, footer length: 22

```
As we can see, it successfully found message.txt. We can use the flag `-e` or `--extract` to automatically extract known file types.

However, as we mentioned previously, simply appending a ZIP file to an image doesn't hide its contents. 

If the ZIP archive contains unencrypted printable text, strings may reveal it without extracting the archive. 

That's why we've password-protected the ZIP file. You can find its password by applying what you've learned so far.

Find the hidden ZIP-password, extract the ZIP file, unzip it, and reveal what's hidden inside.

The password submitted to the checker should be wrapped in binwalk{CAPITAL_LETTERS_WITH_UNDERSCORES}.

Once you find it, run /challenge/check and enter the password to get the flag.

