---
name: media-vlad-chat
description: Generate AI-powered social media content through the media.vlad.chat API. Use when the user wants to create short-form social content — stories, carousels, tweets, threads, or AI videos — by calling a running media.vlad.chat instance. Also use when the user mentions "vlad.chat", "vladchat", "media.vlad.chat", "generate a story", "generate a carousel", "generate a tweet video", "generate a thread video", "generate an AI video", "render a transition video", or "start a content generation workflow". Covers the HTTP REST endpoints, the MCP tool interface, and where rendered outputs are written. Requires the service to be running locally (Next.js on :3000, Bun renderer on :3001) and an OpenAI API key.
metadata:
  tags: media, video, content-generation, ai, remotion, api
---

# media.vlad.chat

The `media.vlad.chat` API is a content-generation engine that composes short-form
social media content (images, audio, captions, and videos) using AI services and
renders visual compositions with Remotion.

This skill tells an agent how to invoke the running service, what each endpoint
generates, and where the finished assets and videos land.

## When to use

Use this skill whenever the user asks to generate social content through a
running media.vlad.chat instance — e.g. "make a story about X", "create a tweet
video", "build a carousel", "generate a thread", "generate an AI video", or
"render transition videos".

## Prerequisites

The service must be running and reachable before any request will do real work:

1. Next.js API server on port **3000**: `npm run dev`
2. Bun renderer on port **3001**: `npm run serve:render`
3. `OPENAI_API_KEY` set in `.env` (required for all AI generation)

## Quick start

```bash
# Start a story generation workflow (returns immediately with a runId)
curl "http://localhost:3000/api/story?prompt=two%20friends%20meet%20at%20a%20cafe"

# Start a tweet video (voice: ash | onyx)
curl "http://localhost:3000/api/tweet?content=your%20tweet&voice=ash"

# Start a carousel post
curl "http://localhost:3000/api/carousel?prompt=productivity%20hacks"

# Start a thread video
curl "http://localhost:3000/api/thread?content=your%20thread&voice=onyx"

# Start a full AI video (Sora visuals + dialogue)
curl "http://localhost:3000/api/video?prompt=a%20cyberpunk%20city"
```

All endpoints are **asynchronous**: they start a background workflow and return a
`runId` immediately. Do not expect a finished file in the response — poll the
output locations below until assets appear.

## What you can generate

| Endpoint      | Outputs                                                                 |
| ------------- | ----------------------------------------------------------------------- |
| `/api/story`  | Narrative dialogue (person + shadow), image slide, per-line voiceover MP3s + word-timed caption JSON, and a rendered Story MP4 |
| `/api/carousel` | Story content + one generated cover image, rendered as a JPEG sequence |
| `/api/tweet`  | A single voiced tweet, rendered as a vertical MP4                       |
| `/api/thread` | A voiced thread, rendered as a vertical MP4                             |
| `/api/video`  | Full AI video with per-segment Sora clips (`video-<n>.mp4`), voiceovers, captions, assembled MP4 |
| `/api/mcp`    | Same capabilities via MCP tools (`generate_story`, `generate_carousel`, `generate_tweet`, `generate_thread`, `generate_video`, `render_track_transitions`) |

## Where outputs go

All artifacts are written to the server's local filesystem:

- **`public/`** — AI-generated assets: `slide-<n>.png`, `speech-<n>.mp3`,
  `captions-<n>.json`, `video-<n>.mp4`. Because Next.js serves `public/`, each
  file is immediately fetchable at `http://localhost:3000/<filename>`.
- **`out/`** — finished rendered media (MP4s, stills, JPEG sequences) with
  timestamped names, plus any custom-named outputs like
  `transition-<outId>-to-<candId>-<energyArc>.mp4`.
- **`stories/`** — structured story JSON saved by name.

After kicking off a workflow, check these directories for new files to determine
completion. There is no status endpoint — file appearance is the signal.

## MCP interface

If the caller has an MCP client, connect it to `http://localhost:3000/api/mcp`
to call the tools directly instead of raw HTTP. The full tool input/output
schemas are in [references/api.md](references/api.md).

## Troubleshooting

- **Requests succeed but no files appear** — the background workflow may be
  erroring. Check the console logs of both the Next.js (`npm run dev`) and Bun
  renderer (`npm run serve:render`) terminals. Workflows are async, so errors
  surface in logs, not in the HTTP response.
- **Renderer failures** — confirm the Bun server is up on :3001 and FFmpeg is
  installed (`ffmpeg -version`).
- **AI generation errors** — verify `OPENAI_API_KEY`, model access, and quota.
- **Disk pressure** — generated assets accumulate in `public/` and `out/`;
  clean them periodically.