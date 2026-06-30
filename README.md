# Agent Kunjani plugin

**Agent Kunjani** is a Claude plugin that designs and publishes gamified
training for [Kunjani](https://www.kunjani.co/), a WhatsApp-based, AI-powered
game-based learning platform. It bundles one skill and one hosted MCP server so
Claude can take a training brief all the way to a live, playable Kunjani deck in
a single conversation.

## What's in the box

| Component | Type | What it does |
|---|---|---|
| `agent-k` | Skill | Instructional-design methodology: interviews you for a brief, designs 3-6 learning outcomes, and writes Kunjani activities across the six Suits (Jolt, Advance, Mystery, Oops, Explain, Demonstrate) with a three-block structure (Activity / Suggested Response / Assessment Notes) tuned for Kunjani's AI grader. |
| `kunjani` | MCP server (remote, hosted) | The hosted Kunjani MCP at `https://play.kunjani.co/api/v1/mcp`. Exposes two tools that publish what `agent-k` designs straight into your Kunjani account. |

### MCP tools

The `kunjani` MCP server exposes exactly two tools:

- **`publish_activity_batch`** — Create or update a deck and publish a batch of
  activities into it in one call. This single tool covers the whole build
  (deck + outcomes + activities). Call it with `mode: "publish"` to persist, or
  `mode: "validate"` for a dry run that writes nothing. Input is a structured
  `deck` object plus an `activities` array (and an optional top-level
  `outcomes` array). See the JSON field reference inside the `agent-k`
  `SKILL.md`.
- **`list_decks`** — List the decks you can see, to find an existing deck's `id`
  and pass it back to `publish_activity_batch` for an idempotent update instead
  of creating a duplicate.

## How to use it

1. **Connect the server** — see [SETUP.md](SETUP.md). The server authenticates
   with OAuth 2.1 (Authorization Code + PKCE); your client discovers the auth
   server automatically and opens a browser sign-in. A legacy `knj_` personal
   API token is also accepted as a bearer token.
2. **Invoke the skill** — start a conversation with a training goal, e.g.
   *"Build me a Kunjani deck to train retail floor staff on handling unhappy
   customers."* The `agent-k` skill triggers on this kind of request (and on
   mentions of Kunjani Suits, deck activities, or `@Agent K`). It will walk you
   through a brief, propose learning outcomes for your approval, then draft
   activities.
3. **Publish** — when you approve the activities, Claude calls
   `publish_activity_batch` to create the deck and push them. It reports the
   deck URL (`https://play.kunjani.co/decks/<id>`) so you can open and play it.

## Scope and boundaries

- **One job, end to end:** brief → outcomes → activities → published deck.
- **Media is URL-only over MCP.** This plugin does not bundle Kunjani's
  media-generation tooling (`agent-k-enhance`). Activities can reference media
  by hosted URL (`picture_url`), but file uploads and audio/image generation
  happen in the Kunjani deck-builder UI, not through this plugin.
- **You need a Kunjani account.** The tools act on your own decks under your own
  authorization; the same permissions that apply in the Kunjani app apply here.

## Privacy

The `kunjani` MCP server is a resource server with no identity of its own: your
bearer token is forwarded verbatim to the Kunjani API, and it only ever touches
data your account is already authorized to see. See the
[Kunjani privacy policy](https://kunjani.ai/privacy-policy) for what data is
processed, why, retention, and your controls.

## Links

- Kunjani: https://www.kunjani.co/
- MCP server source and docs live in the Kunjani application repository.
- Issues for this plugin: https://github.com/Hans-Zietsman/kunjani-plugin/issues
