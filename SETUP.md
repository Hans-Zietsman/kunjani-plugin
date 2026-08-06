# Authorize Agent Kunjani

The Agent Kunjani plugin already includes the skill and hosted MCP connection. Do not install either component separately.

After installing the plugin, authorize it to access your Kunjani account once.

## Requirements

- A Kunjani account at [play.kunjani.co](https://play.kunjani.co)
- Permission to create or edit decks

## OAuth sign-in

1. Install and enable the **Agent Kunjani** plugin.
2. Ask Agent Kunjani to list your decks or create a training deck.
3. Your client opens a Kunjani authorization page.
4. Sign in and approve the requested access.

The server uses OAuth 2.1 with Authorization Code and PKCE. Access tokens last one hour and refresh automatically.

## Manual token fallback

Use this only if the client cannot complete OAuth and supports static bearer headers.

1. Open your Kunjani profile.
2. Copy the personal API token beginning with `knj_`.
3. Add it as the Authorization header for the Kunjani MCP server:

```json
{
  "mcpServers": {
    "kunjani": {
      "type": "http",
      "url": "https://play.kunjani.co/api/v1/mcp",
      "headers": {
        "Authorization": "Bearer knj_your_token_here"
      }
    }
  }
}
```

A personal API token carries full access to the account. Treat it as a password. Do not paste a real token into a shared file, screenshot, support request, or public repository.

## Verify the connection

Ask:

```text
Use Agent Kunjani to list my Kunjani decks.
```

The plugin should call `list_decks` and return the decks visible to the signed-in account. If authorization fails, disable and re-enable the plugin, then repeat the request to restart OAuth.

## Endpoint reference

- MCP endpoint: `https://play.kunjani.co/api/v1/mcp`
- Protected resource metadata: `https://play.kunjani.co/.well-known/oauth-protected-resource`
- Authorization server metadata: `https://play.kunjani.co/.well-known/oauth-authorization-server`
