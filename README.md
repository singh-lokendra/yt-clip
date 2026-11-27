# yt-clip

Cut a clip out of a YouTube video, or a local video file, by start and end time.

```sh
brew install singh-lokendra/tap/yt-clip
```

This installs the dependencies too: `yt-dlp`, `ffmpeg` (with `ffprobe`) and `deno` (yt-dlp uses
it to solve YouTube's JS challenges).

## Usage

```sh
yt-clip <url|file> <start> <end> [output-name]
```

- `url|file`: a YouTube (or any yt-dlp supported) URL, or a path to a local video

- `start` / `end`: `HH:MM:SS`, `MM:SS` or seconds
- `output-name`: without extension (default `clip-<start>-<end>`); the clip is written as
  `<output-name>.mp4` in the current directory

```sh
yt-clip "https://youtu.be/eJmpTKQHjP0" 00:34:00 00:38:30 spiritual-talk-clip
yt-clip "https://youtu.be/eJmpTKQHjP0" 34:00 38:30
yt-clip "https://youtu.be/eJmpTKQHjP0" 2040 2310 talk
yt-clip ~/Movies/talk.mkv 1:02:10 1:05:00 talk-part2
```

## How it works

Local files are cut directly with ffmpeg (H.264 + AAC, frame-accurate). For URLs:

1. Tries `yt-dlp --download-sections`, which fetches only the requested range.
2. Checks the result's duration with `ffprobe`. If the section download failed or wrote an empty
   file (this happens for livestreams YouTube is still processing, `live_status=post_live`), it
   downloads the full stream with yt-dlp's native downloader into a temp dir and cuts the clip
   locally with ffmpeg (H.264 + AAC). The temp download is removed on exit.

## Manual install

```sh
brew install yt-dlp ffmpeg deno
curl -fsSL https://raw.githubusercontent.com/singh-lokendra/yt-clip/master/bin/yt-clip -o /usr/local/bin/yt-clip
chmod +x /usr/local/bin/yt-clip
```
