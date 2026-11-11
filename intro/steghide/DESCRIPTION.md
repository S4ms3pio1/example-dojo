## Steghide

`steghide` is a steganography program that is able to hide data in various 
kinds of image and audio files. The JPEG, BMP, WAV and AU file formats are
supported for use as a cover file. There are no restrictions on the format
of the secret data.

### Overview: how does it work?

The secret data is first compressed and encrypted. The passphrase then
seeds a pseudo-random number generator, which picks out a sequence of
pixel positions in the cover file, this is where the data could go.
Positions that already happen to hold the right value by chance are
skipped. For the rest, a graph-matching algorithm pairs up positions so the
data can be embedded by swapping their values with each other, rather than
overwriting them, any leftover positions that can't be paired are
overwritten directly instead.

This is the actual explanation steghide documents in its man page, it's
genuinely a bit dense, so I'd recommend reading `man steghide` directly if
you want the full technical detail rather than relying on a summary.

### Example of how you can hide data using steghide:

```bash
_> steghide embed -cf test.jpeg -ef sec.txt -sf hidden.jpg
Enter passphrase:
Re-Enter passphrase:
embedding "sec.txt" in "test.jpeg"... done
writing stego file "hidden.jpg"... done
```

`-ef`, `--embedfile`, specify the file that will be embedded (the file
that contains the secret message)

`-cf`, `--coverfile`, specify the cover file that will be used to embed
data. The cover file must be in one of the following formats: AU, BMP,
JPEG, or WAV.

`-sf`, `--stegofile`, specify the name for the stego file that will be
created.

### Get info about a file: `steghide info file_name`

```bash
_> steghide info hidden.jpg
"hidden.jpg":
  format: jpeg
  capacity: 3.9 KB
Try to get information about embedded data? (y/n) y
Enter passphrase:
  embedded file "sec.txt":
    size: 17.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```

### Extract a hidden file: `steghide extract -sf file_name`

```bash
_> steghide extract -sf hidden.jpg
Enter passphrase:
wrote extracted data to "sec.txt".

_> cat sec.txt
I know kung fu: Show me.
```

---

Use `steghide` to extract the secret hidden inside `/challenge/image.jpg`.

It will ask for a passphrase, sometimes Enter alone can do the job.

The password is wrapped in `steghide{}`.

Once you find it, run `/challenge/check` and enter the password to get the
flag.

### Going Further

Check out `man steghide` for more information.
