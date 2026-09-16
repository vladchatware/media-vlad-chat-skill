# media.vlad.chat — service reference

Reference for using the live `media.vlad.chat` service to generate social media
content. Base URL: **`https://media.vlad.chat`**. No API key or local setup is
needed.

## Two ways to invoke

1. **MCP tools** — connect an MCP client to `https://media.vlad.chat/api/mcp`.
   Tools are self-describing and the preferred interface for agents.
2. **HTTP endpoints** — direct `GET` requests for scripting or quick tests.

MCP invocation notes: the endpoint is Streamable HTTP — send JSON-RPC POSTs
with `Accept: application/json, text/event-stream`. Lifecycle notifications
(`initialize`, `notifications/initialized`) may return empty bodies; ignore
empty responses instead of treating them as errors.

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

- `outgoingTrackId` (integer > 0, required) — the outgoing track (music.vlad.chat ID)
- `candidateTrackIds` (array of integers, 1–12 entries, required) — candidate
  tracks to transition into (one render per candidate — 1 outgoing + N
  candidates, not pairs)
- `energyArc` (`"preserve" \| "build" \| "release" \| "reset"`, default `"preserve"`)

Missing candidate analyses are scheduled automatically — callers do not need
to pre-analyze tracks, but a batch with un-analyzed candidates takes
correspondingly longer (analysis runs before rendering).

## Response and result retrieval

Every invocation returns a run ID immediately; the work runs in the background.

```json
{ "runId": "wf_abc123" }
```

Poll over MCP until finished, then fetch results:

- `workflow_status` — arg `run_id` (string). Returns RUNNING / COMPLETED / FAILED.
- `workflow_progress` — arg `run_id`. Fine-grained step progress while RUNNING.
- `workflow_result` — arg `run_id`. Finished-media entries, each with a
  `blobUrl` (direct download) and a `url` (public page URL). Treat `blobUrl`
  entries as the completion signal — a "scheduled" status alone is not
  completion.
- `workflow_cancel` — arg `run_id`. Stops a pending/running run.

Notes:

- Tool arguments use snake_case (`run_id`), even though the invoke response
  says `runId`.
- Typical wall-clock: tweet/thread ≈ 1 min per item;
  `render_track_transitions` ≈ 2 min per candidate in the batch.

## Where finished media appears

Generated files are written to the service and served publicly. The exact
location depends on the content type — the reliable way is to read
`workflow_result` for the finished run: each entry carries a `blobUrl` and a
public `url`.

- Story/carousel assets: `https://media.vlad.chat/<filename>`
  (e.g. `slide-0.png`, `speech-0.mp3`, rendered MP4s).
- Transition/backroom renders:
  `https://media.vlad.chat/public/renders/<...>.mp4` plus a direct-download
  `blobUrl` on the Vercel Blob store.