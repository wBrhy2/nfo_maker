# NFO Generator

A browser-based tool that generates [Jellyfin](https://jellyfin.org/)-compatible `.nfo` metadata files for TV episode libraries. No server required — everything runs locally in the browser.

## Requirements

**Chrome 86+ or Edge 86+** — the tool uses the [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API) to read your folder structure and write `.nfo` files directly to disk. Firefox is not supported.

Open `nfo-generator-live.html` directly in your browser. No installation or build step needed.

---

## How It Works

### 1. Folder Selection

Click **Browse** to pick the folder containing your video files. The tool will scan for files with these extensions: `mp4, mkv, avi, mov, wmv, flv, webm, m4v, mpg, mpeg, m2ts, ts`.

- **Scan subfolders recursively** — when enabled, the tool descends into all subdirectories.
- **Existing NFO Files** — choose whether to skip files that already have a `.nfo` sidecar, or overwrite them.

### 2. Show Configuration

- **Default Season Number** — used as the season for any file where a season can't be determined automatically.
- **Show Title** — the title written into the `<showtitle>` tag of every episode NFO.
- **Use selected folder name as show title** — automatically fills Show Title with the name of the selected folder.
- **First-level directories are separate shows** (Multi-Show Mode) — each direct subfolder is treated as a distinct series; the folder name becomes the show title for all episodes within it.

### 3. NFO Fields

Check which XML tags to include in each generated `.nfo` file.

**Always included:**
- `title` — derived from the video filename (extension stripped)

**Included by default:**
- `season`, `episode`, `displayseason`, `displayepisode`, `showtitle`, `plot`

**Optional (unchecked by default):** checking any of these reveals a text input to set a shared default value applied to every file in the batch:
- `aired` — air date (e.g. `2024-01-01`)
- `rating` — numeric rating (e.g. `8.0`)
- `runtime` — duration in minutes (e.g. `45`)
- `director`
- `writer`
- `mpaa` — TV content rating (e.g. `TV-PG`, `TV-14`, `TV-MA`)

### 4. tvshow.nfo

Enable **Create tvshow.nfo file for each series** to also generate a series-level metadata file at the root of each show folder. Jellyfin uses this file to display show-level information.

### 5. Episode & Season Number Detection

The generator tries to extract episode and season numbers from filenames and directory names automatically:

| Pattern | Example |
|---|---|
| `E01`, `e01`, `Episode 01` in filename | `Show.E05.mkv` → Episode 5 |
| `S01E01` in filename | `Show.S02E03.mkv` → Season 2, Episode 3 |
| Trailing number in filename | `Show 12.mkv` → Episode 12 |
| `Season 2` or `S2` in directory name | folder `Season 3/` → Season 3 |

If no episode number is found, files are **auto-numbered** in alphabetical order within their directory, starting from 1.

### 6. NFO Preview

The **NFO Preview** card shows a live example of the XML that will be written to disk, updating as you change options.

### 7. Generating Files

Click **Generate NFO Files** to write `.nfo` sidecars next to each video file. A progress bar and output log show what was created, skipped, or errored.

---

## Bash Script

For users who prefer to run generation on a remote server or in an automated pipeline, the **Bash Script** section generates an equivalent shell script.

- **Generate Bash Script** — renders the script in the output box based on current settings.
- **Download Script** — saves it as `generate_nfo.sh`.
- **Copy Script** — copies it to the clipboard.

The script embeds all current settings (season, fields, show title, default values, etc.) as variables at the top, so it can be edited and re-run without needing the browser tool.

---

## Output Format

Each episode produces a file named `<video-basename>.nfo` alongside the video. Example:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<episodedetails>
    <title>Show S01E03</title>
    <season>1</season>
    <episode>3</episode>
    <displayseason>1</displayseason>
    <displayepisode>3</displayepisode>
    <showtitle>My Show</showtitle>
    <plot>enter plot here</plot>
    <mpaa>TV-14</mpaa>
</episodedetails>
```
