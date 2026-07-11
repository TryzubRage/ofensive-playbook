# whisper

OpenAI's Whisper is an automatic speech recognition (ASR) model. Used in CTF and pentest contexts to transcribe audio files found in upload directories — voice messages, password recordings, or support calls that inadvertently capture credentials.

## Commands Used

### Transcribe a WAV file with the tiny model (CPU-friendly)

<!-- cmd: linux -->
```bash
whisper voice-message.wav --model tiny --language en
```

Used on: **MakeSense**

Transcribed a voice message left by `jake` in the WordPress uploads directory (`/wp-content/uploads/2026/01/`) where he accidentally spoke his password: `ClearLightNiceSmooth4923`.

### Model Size Reference

| Model | VRAM | Speed | Accuracy |
|-------|------|-------|----------|
| `tiny` | ~1 GB | Fastest | Low (sufficient for clear recordings) |
| `base` | ~1 GB | Fast | Medium |
| `small` | ~2 GB | Medium | Good |
| `medium` | ~5 GB | Slow | Better |
| `large` | ~10 GB | Slowest | Best |

On CPU, always start with `tiny` or `base` — the quality is usually enough for clear voice messages.

### Transcribe an MP3 file

<!-- cmd: linux -->
```bash
whisper audio.mp3 --model base --language en
```

### Force English detection (avoid mis-detection)

<!-- cmd: linux -->
```bash
whisper audio.wav --model small --language en --task transcribe
```

### Output to a text file

<!-- cmd: linux -->
```bash
whisper voice-message.wav --model tiny --language en --output_format txt --output_dir ./transcripts
```
