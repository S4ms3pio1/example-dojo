## WAV

* WAV (Waveform Audio File Format) is an uncompressed audio format built on RIFF, storing data in little-endian chunks.
* A standard PCM WAV starts with a 44-byte header (`RIFF`, `fmt `, and `data`) followed by raw audio samples—no checksums, just raw bytes.

You already know the drill—inspect the bytes, look up the spec, and fix what's broken.

