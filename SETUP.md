# Connecting the Kunjani MCP server

This plugin ships a reference to the hosted Kunjani MCP server
(`https://play.kunjani.co/api/v1/mcp`). After installing the plugin you need to
authenticate to it once. There is nothing to install or run locally; the server
is hosted by Kunjani.

## Prerequisites

- A Kunjani account at https://play.kunjani.co with permission to create decks.
- Either: you sign in through the OAuth browser flow (recommended), **or** you
  have a personal `knj_` API token from your Kunjani profile.

## Option A — OAuth (recommended)

The server advertises OAuth 2.1 (Authorization Code + PKCE) via
`/.well-known/oauth-protected-resource` and
`/.well-known/oauth-authorization-server`. A compliant MCP client discovers this
automatically.

1. Install and enable this plugin.
2. The first time Claude tries to use a Kunjani tool, it triggers the OAuth
   flow: a browser window opens to `play.kunjani.co` asking you to authorize the
   connector with these scopes: `decks:read`, `decks:write`, `outcomes:write`,
   `activities:write`.
3. Approve. The browser hands an authorization code back to your client, which
   exchanges it for an access token. You are connected.

Access tokens last one hour and refresh automatically; you will not be asked to
sign in again each session.

## Option B — Personal `knj_` token (manual bearer)

If your client lets you set a static bearer token for an HTTP MCP server, you can
paste a Kunjani personal API token instead of using OAuth:

1. In Kunjani, open your profile and copy your `knj_...` API token.
2. Configure the `kunjani` server with an Authorization header:

   ```jsonc
   {
     "mcpServers": {
       "kunjani": {
         "type": "http",
         "url": "https://play.kunjani.co/api/v1/mcp",
         "headers": { "Authorization": "Bearer knj_your_token_here" }
       }
     }
   }
   ```

A `knj_` token carries full access to your account, so treat it like a password
and prefer OAuth where the client supports it.

## Verify the connection

Ask Claude to list your Kunjani decks. It should call `list_decks` and return
the decks visible to your account. If you get an authorization error, the token
or OAuth grant did not go through; re-run the flow above.

## Endpoints (reference)

- MCP endpoint: `POST https://play.kunjani.co/api/v1/mcp` (JSON-RPC 2.0,
  Streamable HTTP transport).
- Protected-resource metadata:
  `https://play.kunjani.co/.well-known/oauth-protected-resource`
- Authorization-server metadata:
  `https://play.kunjani.co/.well-known/oauth-authorization-server`
