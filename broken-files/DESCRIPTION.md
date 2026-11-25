## Broken Files

Welcome to the Broken Files module.

This module walks you through the internal byte structure of common file formats JPEG, PNG, GIF, WAV, ZIP, MP4, and PDF one format at a time. Each challenge breaks down how a file is laid out under the hood and gives you a corrupted artifact to inspect, repair, and recover.

Work through the challenges in order and **save every challenge password you find** both the technical difficulty and the story in `/challenge/note` build from one challenge to the next, and the final challenge requires pieces from every previous password to unlock.

All of these challenges are solvable using `hexedit` alone except one, which might require you to write a short script.

> **Note:** Challenges require you to view repaired images, listen to audio, or watch video frames—and some GUI viewers are not available inside the Dojo environment. You may want to copy the challenge files to your local machine using `scp`:

```bash
scp -r -i path-to-key hacker@dojo.pwn.college:/challenge/files/* ./
```

Alternatively, you can download files directly from the VS Code editor inside pwn.college (**Workspace → `</>` Code**):

Hold `Ctrl` and click on the files you want to download to select multiple files, then right-click and select **Download** to save them all at once.

Inspect the bytes, read the challenge descriptions, and check the official format specifications to dig deeper.
