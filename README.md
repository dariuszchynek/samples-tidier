# ytwav

Simple macOS CLI toolkit for:
- downloading YouTube audio as WAV (`ytwav`)
- batch processing local WAV files (`wavbatch`)

## What Each Script Does

### `ytwav`
- takes a YouTube URL
- downloads and converts audio to `.wav`
- saves output to `~/Downloads`

### `wavbatch`
- scans a folder for `.wav` files (excluding files that start with `drums_`)
- detects key and estimates BPM
- renames files to:
  - `<KEY>-<BPM>bpm-<original_name>.wav`
- extracts drum-focused segments into:
  - `drums_<file_stem>_01.wav`, `drums_<file_stem>_02.wav`, ...

## Requirements

- macOS
- Homebrew
- tools:
  - `yt-dlp`, `ffmpeg` (for `ytwav`)
  - `ffmpeg`, `ffprobe`, `aubio`, `demucs` (for `wavbatch`)

## Installation

```bash
# 1) Install Homebrew (if missing)
which brew || /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2) Install required tools
brew install yt-dlp ffmpeg aubio

# 3) Install demucs (required by wavbatch)
brew install pipx
pipx install --python /opt/homebrew/bin/python3.11 "git+https://github.com/facebookresearch/demucs.git"
```

Make scripts executable:

```bash
chmod +x /Users/dariuszchynek/Documents/projekty/ytwav/ytwav
chmod +x /Users/dariuszchynek/Documents/projekty/ytwav/wavbatch
```

Optional global commands:

```bash
sudo ln -sf /Users/dariuszchynek/Documents/projekty/ytwav/ytwav /usr/local/bin/ytwav
sudo ln -sf /Users/dariuszchynek/Documents/projekty/ytwav/wavbatch /usr/local/bin/wavbatch
```

## Usage

### Download YouTube audio

```bash
ytwav "https://youtu.be/VIDEO_ID"
```

Result:
- a `.wav` file in `~/Downloads`

### Batch process local WAV files

Run in current folder:

```bash
wavbatch
```

Run on a specific folder:

```bash
wavbatch "/path/to/folder"
```

Rename only (skip drum extraction):

```bash
wavbatch --nodrums "/path/to/folder"
```

## Output Rules (`wavbatch`)

- If BPM cannot be estimated reliably, `000bpm` is used.
- If a filename already starts with a musical key (`C-`, `F#m-`, `Bb-`, etc.), key detection is skipped.
- If a filename starts with `drums_`, key detection is skipped.
- If a generated `drums_*.wav` has exactly the same file size as the source WAV, it is removed (usually means the source is already drums-only).

## Troubleshooting

`zsh: permission denied`

```bash
chmod +x /usr/local/bin/ytwav
chmod +x /usr/local/bin/wavbatch
```

`command not found: ytwav` or `command not found: wavbatch`

```bash
ls -l /usr/local/bin/ytwav
ls -l /usr/local/bin/wavbatch
```

Then restart Terminal.

`ImportError: TorchCodec is required` (demucs)

```bash
pipx runpip demucs install torchcodec
```
