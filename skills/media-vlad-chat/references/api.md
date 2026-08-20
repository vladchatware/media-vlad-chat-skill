# media.vlad.chat API reference

Reference for the HTTP endpoints and MCP tools exposed by a running
`media.vlad.chat` instance. Base URL: `http://localhost:3000`.

## Transport

- REST endpoints live under `/api/*` and accept `GET` with query parameters.
- The MCP server is at `/api/mcp` (streamable HTTP) and exposes the same
  capabilities as registered tools.
- All REST endpoints respond `200` with `{ "runId": "<id>" }` immediately. The
  requested work executes asynchronously in a background workflow.

## REST endpoints

### `GET /api/story`

Starts the story workflow. Generates a narrative dialogue between a person and
their shadow, an image slide, per-line voiceovers, and word-timed captions, then
renders the full Story composition to an MP4.

| Param | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `prompt` | string | yes | Story prompt or theme |

### `GET /api/carousel`

Generates a story and a cover image, then renders the Carousel composition as a
sequence of JPEG frames.

| Param | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `prompt` | string | yes | Carousel prompt or theme |

### `GET /api/tweet`

Voices the given content with TTS and renders a vertical tweet MP4.

| Param | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `content` | string | yes | Tweet text to voice over |
| `voice` | `ash` \| `onyx` | yes | `ash` = teacher, `onyx` = student |

### `GET /api/thread`

Voices the given content and renders a vertical thread MP4.

| Param | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `content` | string | yes | Thread text to voice over |
| `voice` | `ash` \| `onyx` | yes | `ash` = teacher, `onyx` = student |

### `GET /api/video`

Full AI video workflow. Generates a story, per-segment Sora video clips
(`video-<n>.mp4`), voiceovers (`speech-<n>.mp3`), and captions
(`captions-<n>.json`), then renders the assembled Video composition.

| Param | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `prompt` | string | yes | Video prompt or theme |

## MCP tools

Connect an MCP client to `http://localhost:3000/api/mcp`. Registered tools:

| Tool | Inputs | Description |
| ---- | ------ | ----------- |
| `generate_story` | `prompt: string` | Start story generation |
| `generate_carousel` | `prompt: string` | Start carousel generation |
| `generate_tweet` | `content: string`, `voice: "ash" \| "onyx" = "ash"` | Start tweet video |
| `generate_thread` | `content: string`, `voice: "ash" \| "onyx" = "ash"` | Start thread video |
| `generate_video` | `prompt: string` | Start AI video generation |
| `render_track_transitions` | `outgoingTrackId: int > 0`, `candidateTrackIds: int[] (1-12)`, `energyArc: "preserve" \| "build" \| "release" \| "reset" = "preserve"` | Render vertical transition-review videos for one outgoing SoundCloud track vs candidate tracks |

Each tool returns a text result containing the `runId`. Generation runs in the
background.

## Output locations (server filesystem)

| Directory | Contents | Served at |
| --------- | -------- | --------- |
| `public/` | `slide-<n>.png`, `speech-<n>.mp3`, `captions-<n>.json`, `video-<n>.mp4`, `sound.m4a`, `pic.jpeg` | `http://localhost:3000/<filename>` |
| `out/` | Rendered MP4s, PNG stills, JPEG frame sequences, transition videos | — |
| `stories/` | Structured story JSON, named by story | — |

## Workflow behavior

- Workflows are orchestrated with the `workflow` package (`"use workflow"` /
  `"use step"` directives). Starting one returns a `runId` immediately.
- Render jobs POST to `http://localhost:3001/api/render` with a composition id
  (`Story`, `Carousel`, `Tweet`, `Thread`, `Video`, `BackroomTransitionOnly`),
  `inputProps`, and a render type (`video` | `still` | `sequence`).
- Completion is signaled by files appearing in `public/` and `out/` — there is
  no polling/status API.