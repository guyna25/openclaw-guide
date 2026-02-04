---
name: youtube-transcript
description: Fetches transcripts from YouTube via TranscriptAPI (mcporter) — ban-free, hosted. Use when the user wants a transcript from a YouTube link, podcast discovery, transcript check, or scheduled summaries.
---

# YouTube Podcast Transcript

## When to use

- User provides a YouTube video or podcast URL and wants the transcript.
- User sends a **podcast name** and wants to discover options on YouTube, see which have transcripts, and schedule how often to get transcript summaries.
- User asks for captions, subtitles, or spoken content from YouTube.
- Downstream: summarization, search, auto-save to workspace, WhatsApp summary, or recurring updates.

---

## Primary: TranscriptAPI via mcporter

Fetch transcripts via mcporter calling the transcriptapi MCP server:

- Discover tools: `mcporter list transcriptapi --schema`
- Fetch transcript: `mcporter call transcriptapi.<tool_name> video_url=<youtube_url>`

### Workflow when user sends a YouTube link

1. Call transcriptapi via mcporter: `mcporter call transcriptapi.<tool> video_url=<url>`
2. Clean the transcript: remove filler words (um, ah, etc.).
3. Identify different speakers if possible from context.
4. Save as `.txt` in `workspace/transcripts/` (or user-specified path).
5. Optionally send a one-paragraph summary (e.g. via WhatsApp).

### Podcast discovery (user sends podcast name, no URL)

1. **Search YouTube:** `yt-dlp "ytsearch10:<podcast name>" --flat-playlist -j` or YouTube Data API if available. Get candidates: title, channel, URL.
2. **Present options:** Return 5–10 as `# | Title | Channel | URL`. Ask user to pick (by number/URL) or "check all".
3. **Check transcript availability:** For chosen option(s), call transcriptapi via mcporter on one representative video. Report Has transcript (yes/no); if yes, show one-line sample.
4. **Schedule:** Ask frequency (daily, weekly, on_new_episode). Record in `workspace/transcripts/podcast_schedule.json`:

```json
[
  {
    "name": "Podcast display name",
    "channelOrPlaylistId": "UC... or playlist URL",
    "latestCheckedVideoId": "optional",
    "frequency": "weekly",
    "output": "summary_only",
    "delivery": "whatsapp"
  }
]
```

- **output**: `summary_only` | `summary_and_file` | `full_transcript`
- **delivery**: `whatsapp` | `save_only` | etc.

5. **On each scheduled run:** Read `podcast_schedule.json`; for each entry, if update due → fetch transcript(s) via transcriptapi, generate summary, deliver; update `latestCheckedVideoId` and `lastRun`.
6. **First run:** If user wants immediate summary, fetch + summarize now, save/send, then add to `podcast_schedule.json`.

---

## Fallback: Python (when transcriptapi / mcporter unavailable)

```bash
pip install youtube-transcript-api
```

1. Extract video ID from URL (`watch?v=ID` or `youtu.be/ID`).
2. Fetch: `YouTubeTranscriptApi.get_transcript(video_id)` (optionally `languages=["en", "en-US"]`).
3. Plain text: `" ".join(t["text"] for t in transcript)`.
4. Time-coded: `[t['start']] t['text']` per segment.
5. Save to `workspace/transcripts/<video_id>.txt` or user path.

Add short delays when batching to avoid rate limits.

---

## Output preferences

- **Text only:** return or save concatenated transcript.
- **Timestamps:** `[MM:SS] text` per segment.
- **File:** save under `workspace/transcripts/<video_id>.txt`, confirm path.
