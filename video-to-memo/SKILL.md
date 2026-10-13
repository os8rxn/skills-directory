---
name: video-to-memo
description: "Turn a YouTube URL into a memo that replaces watching the video. Use this whenever the user pastes a YouTube link, mentions a video note, video memo, or asks to capture, distill, summarize, or file a YouTube video. Trigger on a bare YouTube URL with no other instruction. Do not just answer in chat; always write the file."
argument-hint: <youtube-url>
allowed-tools: Bash, Read, Write
---

# Video to Memo

After reading the memo, the user does not need to watch the video.

## 1. Fetch

```bash
URL="<youtube-url>"
MEMO_DIR="${VIDEO_MEMO_DIR:-$HOME/wiki}"
D=/tmp/video-to-memo; rm -rf "$D"; mkdir -p "$D"

yt-dlp --skip-download --no-warnings \
  --print "id=%(id)s" --print "title=%(title)s" --print "channel=%(uploader)s" \
  --print "date=%(upload_date)s" --print "duration=%(duration_string)s" \
  "$URL" > "$D/meta.txt" \
  || { echo "STOP: yt-dlp failed. If it was a bot check: quit Chrome, retry with --cookies-from-browser chrome"; exit 1; }
VID=$(grep '^id=' "$D/meta.txt" | cut -d= -f2)

HIT=$(grep -rl "^video_id: $VID" "$MEMO_DIR" --include='*.md' 2>/dev/null | head -1)
[ -z "$HIT" ] || { echo "STOP: already captured -> $HIT"; exit 0; }

# Preference order. "en.*" alone would also match machine translations like en-ar.
for PASS in "--write-sub --sub-lang en.*" \
            "--write-auto-sub --sub-lang en-orig" \
            "--write-auto-sub --sub-lang en" \
            "--write-sub --write-auto-sub --sub-lang .*-orig,hi.*"; do
  # shellcheck disable=SC2086
  yt-dlp --skip-download $PASS --sub-format vtt -o "$D/sub" --no-warnings "$URL" >/dev/null 2>&1
  V=$(ls "$D"/sub*.vtt 2>/dev/null | head -1)
  [ -n "$V" ] && break
done
[ -n "$V" ] || { echo "STOP: no captions in any language. No memo without a transcript."; exit 1; }

python3 - "$V" "$VID" > "$D/transcript.txt" <<'PY'
import sys, re
def sec(t):
    p = [float(x) for x in t.split(":")]
    return p[0]*3600 + p[1]*60 + p[2] if len(p) == 3 else p[0]*60 + p[1]
def link(c, vid):
    c = int(c); h, m, s = c//3600, (c%3600)//60, c%60
    label = f"{h}:{m:02d}:{s:02d}" if h else f"{m}:{s:02d}"
    return f"[{label}](https://youtu.be/{vid}?t={c})"
cur = None; last = -999; prev = None; out = []
for l in open(sys.argv[1], errors="replace"):
    l = l.strip()
    m = re.match(r"((?:\d+:)?\d+:\d+\.\d+)\s*-->", l)
    if m: cur = sec(m.group(1)); continue
    l = re.sub(r"<[^>]+>", "", l).strip()
    if not l or l.startswith(("WEBVTT", "Kind:", "Language:")) or l.isdigit() or l == prev: continue
    if cur is not None and cur - last >= 30:
        out.append(link(cur, sys.argv[2])); last = cur
    out.append(l); prev = l
print(" ".join(out))
PY

cat "$D/meta.txt"
echo "captions=$(basename "$V")"
echo "words=$(wc -w < "$D/transcript.txt" | tr -d ' ')"
echo "memo_dir=$MEMO_DIR"
SC=""
for S in "$HOME/.claude/skills/samir-comms" "$HOME/.agents/skills/samir-comms"; do
  [ -f "$S/SKILL.md" ] && { SC="$S"; break; }
done
if [ -n "$SC" ]; then echo "voice=samir-comms"; echo "skill=$SC/SKILL.md"; [ -f "$SC/slop_lint.py" ] && echo "lint=$SC/slop_lint.py"; else echo "voice=ste"; fi
```

The script stops on its own if the video is already captured or has no
captions. Otherwise it prints `key=value` lines and writes
`/tmp/video-to-memo/transcript.txt`, which carries a ready-made link every 30
seconds, like `[14:32](https://youtu.be/ID?t=872)`. Copy links verbatim. Never
invent a time.

- `captions=` without `en` means a non-English source.
- `memo_dir=` is where the memo goes.
- `voice=` picks the register for Step 2. `skill=` is the samir-comms file to
  read. `lint=` is its checker, present only when that install ships it.

## 2. Write

Read the transcript. Write `<memo_dir>/<upload date>-<4-word-slug>.md`:

```markdown
---
title: <title>
url: https://youtu.be/<id>
video_id: <id>
channel: <channel>
published: <YYYY-MM-DD>
duration: <duration>
tags: [video, <2-3 topics>]
---

## TL;DR
<What the speaker argues, in 3 bullets.>

## Walkthrough
<Prose that follows the speaker's arc, one ### subhead per major move, each
opened with its transcript link. Carry the reasoning and the examples, so the
reader could defend the argument without the video.>

## Key claims
<4 to 6 facts worth quoting a year from now, outside this video: numbers,
dates, named frameworks. One transcript link each.>
```

## 3. Rules

1. Write the substance, never a pointer to it. "He explains why X" is a hard
   fail. Write why X.
2. Non-English captions: write in English and add `> Transcript language: <lang>`
   under the frontmatter. Under 300 transcript words: add `> Low-confidence
   transcript`. Garbled numbers: drop them, never guess.
3. Voice, as reported by `voice=`:

   **samir-comms**: read `<skill>`, write in that voice, doc channel. If a
   `lint=` line was printed, gate the file before you report it:

   ```bash
   python3 <lint> --gate 15 <memo_dir>/<file>.md
   ```

   On FAIL, read `references/slop-patterns.md` next to `<lint>`, fix the
   flagged lines, rerun. No `lint=` line: skip the gate.

   **ste**: write in ASD-STE100 Simplified Technical English. One topic per
   sentence. 20 words per sentence at most. Active voice, present tense, a
   named subject. No -ing verb forms. No noun strings longer than 3 words. One
   meaning per word, the everyday one. 6 sentences per paragraph at most. Add
   `> Voice: ASD-STE100` under the frontmatter.

Print the file path, word count, and lint score, or `lint: n/a` when there was
no gate. Nothing else.
