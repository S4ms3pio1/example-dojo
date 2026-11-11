## strings


`strings` what a command Ha, I know you are going to skip this... just in case, 
it scans a file and prints every run of printable characters it
finds, regardless of the file's type. It doesn't need to understand the
file's structure at all, just raw bytes.

Example:
```
strings -n 10 file.bin
```

`-n` sets the **minimum length** of a printable run to print. By default,
`strings` only shows runs of **4 or more** characters, anything shorter is
silently skipped. If you know (or suspect) the string you're looking for is
short, lower this with `-n`, or you'll miss it entirely even though it's
sitting right there in the file.



Find the password embedded in `/challenge/image.png`.

The password is wrapped in `strings{}`.

Once you find it, run `/challenge/check` and enter the password to get the
flag.
