---
name: media-vlad-chat
description: Generate AI-powered short-form social media content through the live media.vlad.chat service. Use when the user wants to create content like a narrated story video, a carousel post, a tweet video, a thread video, or a full AI-generated video by describing an idea or prompt. Also use when the user mentions "vlad.chat", "media.vlad.chat", "generate a story", "make a carousel", "turn this tweet into a video", "turn this thread into a video", "make an AI video", or "create social content". The skill tells the agent how to invoke the live service, which content types are available, how to pick the right one for the user's idea, and how to get the finished result. No local setup or API keys are required — the service runs at https://media.vlad.chat.
metadata:
  tags: media, social-media, content-generation, video, ai, stories
---

# media.vlad.chat

A live AI content studio that turns an idea into finished social media content.
You send it a prompt, it writes the story or script, generates images, voiceover
audio, captions, and even video clips, then renders it into shareable media
(MP4s, images, carousels).

This is a **product skill**: it teaches an agent how to act as a user of the
service, not how to run or extend its codebase.

## When to use

Use this skill when the user wants to produce social media content. If they say
things like:

- "I want a story video about *[topic]*"
- "Make a carousel post for my brand about *[topic]*"
- "Turn this tweet into a video" or "read this tweet aloud"
- "Turn this thread into a video"
- "Generate an AI video of *[scene/idea]*"
- "Create content for my Instagram/TikTok/Twitter/Threads"

…then this skill tells you how to get it made through the live service.

## How it works

1. The user describes what they want (a topic, a script, an existing tweet or
   thread, or just a vibe).
2. The agent picks the right content type (below) and calls the service.
3. The service generates everything in the background: script/story, images,
   voiceover audio, captions, and/or video clips.
4. Finished media is written to the service and served back to the user at a
   shareable URL.

## Content types

| Want this…                    | Use tool / endpoint            | You get                                                                 |
| ------------------------------ | ------------------------------ | ----------------------------------------------------------------------- |
| A narrated story video         | `generate_story` / `/api/story` | Story with image slides, per-line voiceovers, captions, rendered MP4     |
| A carousel for a feed          | `generate_carousel` / `/api/carousel` | Story content + cover image, rendered as carousel frames          |
| A tweet read aloud             | `generate_tweet` / `/api/tweet` | Voiced tweet, rendered as a vertical MP4                                |
| A thread read aloud            | `generate_thread` / `/api/thread` | Voiced thread, rendered as a vertical MP4                             |
| A full AI video                | `generate_video` / `/api/video` | AI-generated visuals (Sora) + dialogue, assembled into an MP4            |
| Transition video for music     | `render_track_transitions` (MCP only) | Vertical transition-review videos for SoundCloud track pairs   |

## How to invoke the service

The cleanest way is through the MCP tools at **`https://media.vlad.chat/api/mcp`**.
If the agent has an MCP client, connect it and call the tools directly — the
tools are self-describing. Alternatively, call the HTTP endpoints directly:

```bash
# Story video
curl "https://media.vlad.chat/api/story?prompt=two%20friends%20reunite%20at%20a%20cafe"

# Carousel post
curl "https://media.vlad.chat/api/carousel?prompt=productivity%20hacks"

# Tweet video (voice: ash=teacher, onyx=student)
curl "https://media.vlad.chat/api/tweet?content=your%20tweet%20text&voice=ash"

# Thread video
curl "https://media.vlad.chat/api/thread?content=your%20thread&voice=onyx"

# Full AI video
curl "https://media.vlad.chat/api/video?prompt=a%20cybernetic%20city%20at%20sunrise"
```

Each call returns a run ID immediately and the work continues in the background.

## Guiding the user

- Help the user choose the right content type: a personal message works well as
  a story; a product or tips list works well as a carousel; existing tweets or
  threads convert directly into voiced videos; a cinematic idea suits a full AI
  video.
- For tweet/thread videos, offer the voice choice: **ash** (warm teacher) or
  **onyx** (energetic student).
- Generation takes a little while — tell the user it's working and share the
  finished media URL when it's ready.

## Where the result lives

Generated media is written to the service and served publicly. Once a job
finishes, the finished video or image is available at a `media.vlad.chat` URL
(the raw assets are served from `https://media.vlad.chat/<filename>`). Fetch or
share that URL with the user.

## Troubleshooting

- **Nothing appears after a while** — generation runs in the background; recheck
  the output location or have the user retry with a clearer prompt.
- **Errors on invocation** — the service needs a valid prompt (or `content` +
  `voice` for tweet/thread). Re-read the tool schema for the exact required
  inputs.
- **Service unreachable** — if `https://media.vlad.chat` does not respond, the
  service may be down; the agent can offer to run a local instance from the
  source repo instead.