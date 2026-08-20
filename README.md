# media-vlad-chat-skill

Agent skill for generating AI-powered social media content through the
[media.vlad.chat](https://github.com/vladchatware/media.vlad.chat) API.

This repo exists purely to distribute the skill — it contains no application
code. The API itself lives in the main `media.vlad.chat` repository.

## Install

```bash
bunx skills add vladchatware/media-vlad-chat-skill --skill media-vlad-chat
```

For a specific agent: `bunx skills add vladchatware/media-vlad-chat-skill --skill media-vlad-chat -a codex`

## What the skill does

Teaches an agent how to invoke a running `media.vlad.chat` instance to generate
short-form social content: stories, carousels, tweet videos, thread videos, full
AI videos, and transition renders. Covers the REST endpoints, the MCP tool
interface, and where rendered outputs are written.

## Requirements

The service must be running locally:

1. Next.js API on port `3000`: `npm run dev`
2. Bun renderer on port `3001`: `npm run serve:render`
3. `OPENAI_API_KEY` set in `.env` (required for all AI generation)

See the [SKILL.md](skills/media-vlad-chat/SKILL.md) for full usage and
[references/api.md](skills/media-vlad-chat/references/api.md) for the endpoint
and tool schemas.

## Source of truth

Keep the skill files in this repo (`skills/media-vlad-chat/`). Do not duplicate
them into the app repo — it contains generated media and should not be a skill
distribution surface.

## License

ISC