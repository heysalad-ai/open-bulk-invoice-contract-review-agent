# Bulk Invoice and Contract Review

Runnable Cloudflare Worker and curl-first example for the OpenAI Agents API. It creates a reusable agent named `Bulk invoice and contract review`, starts a streamed session from the returned `agent_id`, and streams raw session events.

## Files

- `config/agent-definition.json` contains the reusable agent definition.
- `config/session-input.txt` contains the initial user message.
- `scripts/run-agent-session.sh` calls the Agents HTTP API directly with `curl`.
- `src/index.ts` is a Cloudflare Worker UI/API layer that mirrors the same flow.

## Setup

```bash
npm install
export OPENAI_API_KEY="your-api-key"
```

The app uses OpenAI project `proj_mRsQVx3NjOamxeXH6UrLowoC` via the `OpenAI-Project` header by default.

## Run Locally

```bash
npm run run:agent
npm run typecheck
npm run dev
```

## Deploy To Cloudflare Workers

The Worker name is `open-bulk-invoice-contract-review-agent`. Keep that name. Deploying as `bulk-invoice-contract-review-agent` would replace a different Worker.

```bash
npx wrangler secret put OPENAI_API_KEY
npx wrangler secret put SESSION_AUTH_SECRET
npm run deploy
```

`SESSION_AUTH_SECRET` must be a random string of at least 32 characters. Set it with Wrangler and do not commit it. `POST /api/sessions` requires `Authorization: Bearer <SESSION_AUTH_SECRET>`.

If that secret is missing or shorter than 32 characters, session creation returns 503. Each attempt is counted before that check, per `CF-Connecting-IP` (`ip:` plus the header, or `ip:unknown` when the header is missing), at 10 requests per 60 seconds. Authorized session creation is also capped at 10 per 60 seconds for the whole Worker.

Default Agents API environment is `openai_hosted`. Set `AGENTS_ENVIRONMENT_TYPE=none` only when no sandbox is needed.

## Tests

```bash
npm test
```

## License

Copyright (C) 2026 HeySalad Inc.

Licensed under the GNU Affero General Public License v3.0 only (AGPL-3.0-only). See [LICENSE](LICENSE) and [NOTICE](NOTICE). If you run a modified version of this software to provide a network service, the AGPL requires you to offer its users the corresponding source code. Versions published before this change remain under their previous licence.
