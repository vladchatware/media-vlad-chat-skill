# media.vlad.chat — service reference

Reference for using the live `media.vlad.chat` service to generate social media
content. Base URL: **`https://media.vlad.chat`**. No API key or local setup is
needed.

## Two ways to invoke

1. **MCP tools** — connect an MCP client to `https://media.vlad.chat/api/mcp`.
   Tools are self-describing and the preferred interface for agents.
2. **HTTP endpoints** — direct `GET` requests for scripting or quick tests.

## Content types and what you get

| Content type | Tool (MCP) | Endpoint | Output |
| ------------ | ---------- | -------- | ------ |
| Story video | `generate_story` | `GET /api/story?prompt=...` | Narrated story with image slides, voiceover per line, captions, rendered MP4 |
| Carousel | `generate_carousel` | `GET /api/carousel?prompt=...` | Story + cover image rendered as carousel frames |
| Tweet video | `generate_tweet` | `GET /api/tweet?content=...&voice=...` | Voiced tweet, vertical MP4 |
| Thread video | `generate_thread` | `GET /api/thread?content=...&voice=...` | Voiced thread, vertical MP4 |
| AI video | `generate_video` | `GET /api/video?prompt=...` | Sora-generated visuals + dialogue, assembled MP4 |
| Transition video | `render_track_transitions` | MCP only | Vertical transition-review videos for SoundCloud track pairs |

## Tool schemas (MCP)

### generate_story

- `prompt` (string, required) — the story prompt or theme

### generate_carousel

- `prompt` (string, required) — the carousel prompt or theme

### generate_tweet

- `content` (string, required) — tweet text to voice over
- `voice` (`"ash" \| "onyx"`, default `"ash"`) — `ash` = teacher, `onyx` = student

### generate_thread

- `content` (string, required) — thread text to voice over
- `voice` (`"ash" \| "onyx"`, default `"ash"`) — `ash` = teacher, `onyx` = student

### generate_video

- `prompt` (string, required) — the video prompt or theme

### render_track_transitions

- `outgoingTrackId` (integer > 0, required) — the outgoing SoundCloud track
- `candidateTrackIds` (array of integers 1–12, required) — candidate tracks to
  transition into
- `energyArc` (`"preserve" \| "build" \| "release" \| "reset"`, default `"preserve"`)

## Response

Every invocation returns a run ID immediately; the work runs in the background.

```json
{ "runId": "wf_abc123" }
```

## Where finished media appears

Generated files are written to the service and served publicly. Assets are
available at `https://media.vlad.chat/<filename>` (e.g. `slide-0.png`,
`speech-0.mp3`, rendered MP4s). Check that location for the finished result.