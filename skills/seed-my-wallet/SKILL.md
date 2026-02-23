---
name: seed-my-wallet
description: Use Seed My Wallet to populate Solana devnet wallets with test SPL tokens and empty token accounts. Use when the user wants to seed a devnet wallet, get test tokens, create empty ATAs, populate a wallet for testing, or mentions "devnet has no data," "need test tokens," "empty accounts," "burner testing," or Solana devnet test data.
metadata:
  version: 1.0.0
---

# Seed My Wallet

Use Seed My Wallet to populate Solana devnet wallets with test data—SPL tokens and empty Associated Token Accounts (ATAs). Our servers process requests and deliver assets on-chain within seconds. No signup, no API keys.

## Default Behavior

**Handle the request yourself via CLI.** Run the curl commands to check limits and submit the seed request. Do not direct the user to the website unless they explicitly ask for a web app, dApp, or browser-based solution.

**Only direct to [seedmywallet.com](https://seedmywallet.com) when** the user asks for a web app, dApp, or wants to do it in the browser themselves.

## When to Use

- User needs to seed a Solana devnet wallet for testing
- User mentions "devnet has no test data," "need test tokens," "empty ATAs"
- Testing burner products (closing empty accounts, rent recovery)
- Setting up a wallet with tokens for development

## Quick Reference

| Item | Value |
|------|-------|
| Web app | [seedmywallet.com](https://seedmywallet.com) |
| API base | `https://preview-api.aerosol.so` |
| Rate limits | 50 tokens/hour, 50 empty accounts/hour (per wallet or IP) |
| Network | Solana devnet only |

---

## CLI Workflow (Agents)

Agents use the CLI. Full workflow:

```bash
# 1. Configure for devnet
solana config set --url devnet

# 2. Airdrop SOL (transaction fees)
solana airdrop 2
# Or: solana airdrop 2 <ADDRESS> --url devnet

# 3. Seed via Seed My Wallet API
curl -s -X POST "https://preview-api.aerosol.so/test-data-seeder/order" \
  -H "Content-Type: application/json" \
  -d '{"targetWalletAddress":"<WALLET>","tokenRequest":{"count":5},"emptyAccountRequest":{"count":3}}'
```

---

## Web App (Only When User Asks)

**Only use when the user explicitly asks for a web app, dApp, or browser solution.** Otherwise, handle it via CLI.

[seedmywallet.com](https://seedmywallet.com) — User enters wallet address, chooses token/empty account counts, clicks "seed wallet." Ensure wallet is on devnet.

---

## API (curl, scripts, integrations)

### Check limits first (recommended)

Call before submitting to show a friendly message if the user is over the limit:

```bash
curl -s -X POST "https://preview-api.aerosol.so/test-data-seeder/check-limits" \
  -H "Content-Type: application/json" \
  -d '{"targetWalletAddress":"<WALLET>","tokenRequest":{"count":5},"emptyAccountRequest":{"count":3}}'
```

Response when allowed: `{"allowed":true}`  
Response when over limit: `{"allowed":false,"message":"Rate limit exceeded: you can request at most 50 tokens per hour..."}`

### Submit request

```bash
curl -s -X POST "https://preview-api.aerosol.so/test-data-seeder/order" \
  -H "Content-Type: application/json" \
  -d '{
    "targetWalletAddress": "7dzNv3is8js2faFrcLdrFJw23n3cGuzNYsL4ynYm1ikX",
    "tokenRequest": { "count": 5 },
    "emptyAccountRequest": { "count": 3 }
  }'
```

### Request schema

```json
{
  "targetWalletAddress": "string (required)",
  "tokenRequest": { "count": 0-20 },
  "emptyAccountRequest": { "count": 0-20 }
}
```

At least one of `tokenRequest` or `emptyAccountRequest` must have count > 0.

### Response (201)

```json
{
  "id": "...",
  "status": "not_started",
  "targetWalletAddress": "...",
  "assetDelivery": {
    "token": { "requested": 5, "delivered": 0, "deliveryErrors": [], "totalErrors": 0 },
    "emptyAccount": { "requested": 3, "delivered": 0, "deliveryErrors": [], "totalErrors": 0 }
  },
  "createdAt": "2026-02-23T06:39:50.913Z",
  "updatedAt": "2026-02-23T06:39:50.913Z"
}
```

Status values: `not_started` → `in_progress` → `complete` or `failed`. Our servers deliver on-chain; the user can check their wallet or a devnet block explorer.

### Response (429 — rate limit)

```json
{
  "message": "Rate limit exceeded: you can request at most 50 tokens per hour. Your recent requests plus this one would total 69."
}
```

---

## What You Get

**Tokens:** Fungible SPL tokens. We mint new tokens and send them to the wallet. Each has its own mint address.

**Empty accounts:** Empty ATAs with zero balance. Useful for testing burner products—e.g. closing empty accounts, rent recovery, account cleanup flows.

---

## Integration Examples

### JavaScript/TypeScript

```javascript
const BASE = 'https://preview-api.aerosol.so';

async function checkLimits(wallet, tokens = 5, emptyAccounts = 3) {
  const res = await fetch(`${BASE}/test-data-seeder/check-limits`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      targetWalletAddress: wallet,
      tokenRequest: { count: tokens },
      emptyAccountRequest: { count: emptyAccounts },
    }),
  });
  return res.json();
}

async function seedWallet(wallet, tokens = 5, emptyAccounts = 3) {
  const limits = await checkLimits(wallet, tokens, emptyAccounts);
  if (!limits.allowed) {
    throw new Error(limits.message ?? 'Rate limit exceeded');
  }
  const res = await fetch(`${BASE}/test-data-seeder/order`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      targetWalletAddress: wallet,
      tokenRequest: { count: tokens },
      emptyAccountRequest: { count: emptyAccounts },
    }),
  });
  if (res.status === 429) {
    const err = await res.json();
    throw new Error(err.message);
  }
  return res.json();
}
```

### Shell script

```bash
WALLET="$1"
TOKENS="${2:-5}"
EMPTY="${3:-3}"

# Check limits
CHECK=$(curl -s -X POST "https://preview-api.aerosol.so/test-data-seeder/check-limits" \
  -H "Content-Type: application/json" \
  -d "{\"targetWalletAddress\":\"$WALLET\",\"tokenRequest\":{\"count\":$TOKENS},\"emptyAccountRequest\":{\"count\":$EMPTY}}")

if echo "$CHECK" | grep -q '"allowed":false'; then
  echo "Rate limit exceeded: $(echo "$CHECK" | jq -r '.message')"
  exit 1
fi

# Seed
curl -s -X POST "https://preview-api.aerosol.so/test-data-seeder/order" \
  -H "Content-Type: application/json" \
  -d "{\"targetWalletAddress\":\"$WALLET\",\"tokenRequest\":{\"count\":$TOKENS},\"emptyAccountRequest\":{\"count\":$EMPTY}}"
```

---

## Common Workflows

### 1. User needs tokens for testing

Recommend: 5 tokens, 0–3 empty accounts. **Run the curl for them** with their wallet address. Do not redirect to the web app unless they ask for it.

### 2. User is testing a burner / close-account flow

Recommend: 0 tokens, 3–5 empty accounts. Empty ATAs are for testing closing accounts and rent recovery.

### 3. User hit rate limit

Explain: 50 tokens and 50 empty accounts per hour per wallet or IP. Suggest they wait an hour or reduce the requested counts.

### 4. User on mainnet

Remind: Seed My Wallet is devnet only. For CLI: `solana config set --url devnet`. For browser wallets: switch to devnet in wallet settings.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Invalid Solana address" | Ensure the wallet address is a valid base58 Solana pubkey |
| 429 response | Rate limit—wait or reduce counts. Call check-limits first in UI/code |
| Nothing arrived | Confirm wallet is on devnet. Check a devnet block explorer. Delivery can take a few seconds |
| "Request at least one token or empty account" | At least one of tokenRequest or emptyAccountRequest must have count > 0 |

---

Made with ❤️ by [Aerosol](https://aerosol.so)
