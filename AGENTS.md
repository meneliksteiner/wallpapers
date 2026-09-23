# Wallpapers

A collection of desktop wallpapers, sorted by brightness so they can be matched to a light or dark system theme.

## Structure

```
light/   bright wallpapers, for a light theme
dark/    dark wallpapers, for a dark theme
```

Every image lives in exactly one of these two folders. Do not create other folders or subfolders.

## Categorizing

Use the image's average brightness (0 = black, 1 = white):

```sh
magick "<image>[0]" -resize 400x400 -colorspace Gray -format '%[fx:mean]\n' info:
```

- `>= 0.45` → `light/`
- `< 0.45` → `dark/`

Use the measured value; do not judge by eye.

## Naming

Files are named `YYYY-MM-DD-NN.ext`: the date the image was added to the repo, a two-digit counter for that day, and a lowercase extension. For example `2026-09-23-01.jpg`, `2026-09-23-02.webp`.

- The date is the day the image is added, not when it was created or downloaded.
- The counter starts at `01` each day and is shared by both folders, so a name appears in only one folder.
- A new image gets the highest counter ever used for that day, plus one (or `01` if there is none). Find it with:

  ```sh
  d=$(date +%F)
  { git log --all --format= --name-only; ls light dark; } | grep -oE "(^|/)$d-[0-9]{2}\." | grep -oE '[0-9]{2}\.$' | tr -d . | sort -n | tail -1
  ```

  This also counts deleted files, so a removed name is never handed out again.
- Never rename existing files and never fill gaps. Gaps from removed images are expected.
- No descriptive names, artist names, resolutions or source IDs in the file name.
- Keep the original image format; do not convert. Lowercase the extension and use `.jpg` instead of `.jpeg`.

## Adding and removing images

- Add: measure brightness, pick the folder, then move the file in under today's date and the next counter.
- Remove: delete the file (`git rm`). Leave every other file as it is.
- Moving or renaming tracked files: use `git mv` so history follows the file.

Every add, remove or move must update `README.md` in the same change (see below).

## README.md

`README.md` embeds every image so the collection can be previewed on GitHub. It must always list exactly the files in `light/` and `dark/`.

- Each image is one line in its folder's section (`## Light` or `## Dark`), sorted by file name:

  ```html
  <a href="dark/2026-09-23-01.jpg" target="_blank"><img src="dark/2026-09-23-01.jpg" alt="2026-09-23-01" title="2026-09-23-01" width="32%"></a>
  ```

  Always include `target="_blank"` so the full-size image opens in a new window. GitHub strips this attribute when rendering, but other Markdown viewers honour it, so keep it on every line.
- Adding an image: add its line in the right place.
- Removing an image: delete its line, and its row in the Credits table if it has one.
- Moving an image between folders: move its line to the other section and update the path in the Credits table.
- Credits: the original file name often contains the artist's name or a source ID (for example `alena-aenami-escape.jpg` or `wallhaven-mdjrqy.jpg`). Before renaming a new image, add a row for it to the Credits table with the artist or source. Only add what the file name or the user actually tells you; do not guess artists. For wallhaven IDs, link to `https://wallhaven.cc/w/<id>`.
- Keep the credit note at the top of the README unchanged.

Check that the README matches the folders (no output from `diff` means they match):

```sh
diff <(grep -oE 'img src="[^"]+"' README.md | cut -d'"' -f2 | sort) <(ls light/* dark/* | sort)
```
