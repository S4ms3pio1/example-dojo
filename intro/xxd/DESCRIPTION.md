## xxd

`xxd` is a command-line utility used to create and analyze hexadecimal dumps of files.

Every byte can be represented by two hexadecimal digits.

For example:

```text
48 65 6c 6c 6f
```

Represents:

```text
Hello
```

You can inspect a file's raw bytes using:

```bash
xxd mystery.bin
```


I bet you do not know that You can also reverse a hexadecimal dump using:

```bash
_> cat sec.txt 
49 74 27 73 20 61 20 55 4e 49 58 20 73 79 73 74 65 6d 21 20 49 20 6b 6e 6f 77 20 74 68 69 73 21
_> xxd -r -p sec.txt 
It's a UNIX system! I know this!
```

Find the hidden password inside `/challenge/mystery.bin`.

The password consists of hex bytes supplied as a continuous string (such as "abcd", with no spaces or dashes), wrapped in xxd{}.

Once you find it, run `/challenge/check` and enter the password to get the flag.
