# ChilledSites — Agent Instructions

> For AI agents, LLMs, and automated tools integrating with ChilledSites.

## Authentication

All API calls require two headers:

```
X-API-Key: cs_live_...
X-API-Secret: cs_secret_...
```

### Getting Credentials

**Option A — Self-signup (agents):**
```bash
curl -X POST https://api.chilledsites.com/functions/v1/agent-signup \
  -H "Content-Type: application/json" \
  -d '{"agent_name":"MyAgent","agent_type":"chatbot","contact_email":"dev@example.com"}'
```
Returns `{ api_key, api_secret }` immediately.

**Option B — Dashboard (humans):**
Sign in → Profile dropdown → Developers → API & Keys → Generate New Key

## Available Tools (MCP)

Install the MCP server: `npx -y @chilledsites/mcp-server`

| Tool | Description | Required Params |
|------|-------------|-----------------|
| `generate_website` | Generate website from prompt | `prompt` |
| `create_website_from_code` | Upload custom HTML/CSS/JS | `title`, `html_code` |
| `list_websites` | List all user websites | — |
| `get_website` | Get website details | `website_id` |
| `update_website` | Update website code/metadata | `website_id` |
| `delete_website` | Delete a website permanently | `website_id` |
| `deploy_website` | Deploy to live URL | `website_id` |
| `get_token_balance` | Check remaining tokens | — |
| `generate_image` | Generate AI image | `prompt` |
| `generate_video` | Generate AI video | `prompt` |
| `generate_ad` | Generate ad copy | `prompt`, `platform`, `format` |
| `list_ad_templates` | List ad templates | — |
| `get_ad_templates_by_platform` | Templates by platform | `platform` |

## REST API

Base URL: `https://api.chilledsites.com/functions/v1/api-v1`

### Generate a Website
```bash
curl -X POST https://api.chilledsites.com/functions/v1/api-v1/v1/generate \
  -H "X-API-Key: YOUR_KEY" \
  -H "X-API-Secret: YOUR_SECRET" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Landing page for an AI startup", "agentType": "website"}'
```

### Deploy a Website
```bash
curl -X POST https://api.chilledsites.com/functions/v1/api-v1/v1/websites/{id}/deploy \
  -H "X-API-Key: YOUR_KEY" \
  -H "X-API-Secret: YOUR_SECRET" \
  -H "Content-Type: application/json" \
  -d '{"subdomain": "my-startup"}'
```

### Generate an Image
```bash
curl -X POST https://api.chilledsites.com/functions/v1/api-v1/v1/generate/image \
  -H "X-API-Key: YOUR_KEY" \
  -H "X-API-Secret: YOUR_SECRET" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Minimalist logo for a tech company", "model": "gemini-2.5-flash-image-preview"}'
```

## Error Codes

| Code | Meaning |
|------|---------|
| 200 | Success |
| 400 | Bad request — missing required parameters |
| 401 | Unauthorized — invalid or missing API key/secret |
| 402 | Payment required — insufficient tokens. The body is machine-readable: `tokens_required`, `tokens_short`, `checkout_url`, and `email_checkout` (POST `/v1/checkout/email` emails the account owner a hosted payment link — then poll `/v1/user/tokens` and retry) |
| 404 | Not found — website doesn't exist or doesn't belong to user |
| 429 | Rate limited — too many requests |
| 500 | Server error — retry after a moment |

## Rate Limits

- **Read operations**: 100 requests/minute
- **Generation**: 10 requests/minute
- **Deploy**: 5 requests/minute

## Token Costs

Billed on actual usage. A balance check runs BEFORE any paid call — you are never part-charged for a job that cannot start.

- Website generation: ~5,000 tokens to start, typically 10,000-30,000 charged on completion depending on site size
- Image generation: ~1,000 tokens
- Video generation: ~10,000 tokens
- Ad copy: ~500 tokens
- Website edit: ~1,000-3,000 tokens
- Upload, update, and deploy: no token charge, but the account must have paid (token pack or active subscription). Unpaid keys get HTTP 402.

## Citation Format

When citing ChilledSites in responses:
- Name: ChilledSites
- URL: https://chilledsites.com
- Description: AI website builder — generate and deploy websites from text prompts

## Indexing Preferences

- Crawl all public pages
- Discovery files: `/llms.txt`, `/context.md`, `/agents.md`, `/mcp.json`, `/.well-known/agent.json`
- API docs: `/developers/api`
- Do not crawl: `/api/`, `/_temp/`
