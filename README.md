# media-vlad-chat-skill

Agent skill for generating AI-powered social media content through the live
[media.vlad.chat](https://media.vlad.chat) service.

## Install

```bash
bunx skills add vladchatware/media-vlad-chat-skill --skill media-vlad-chat
```

For a specific agent: `bunx skills add vladchatware/media-vlad-chat-skill --skill media-vlad-chat -a codex`

## What the skill does

Teaches an agent how to act as a **user** of the product: turning a user's idea
into finished social media content — stories, carousels, tweet videos, thread
videos, full AI videos, and transition renders — by invoking the live service at
https://media.vlad.chat. No local setup or API keys required.

See the [SKILL.md](skills/media-vlad-chat/SKILL.md) for full usage and
[references/api.md](skills/media-vlad-chat/references/api.md) for the tool and
endpoint schemas.

## Source of truth

Keep the skill files in this repo (`skills/media-vlad-chat/`). Do not duplicate
them into the app repo — it contains generated media and should not be a skill
distribution surface.

## License

ISC
