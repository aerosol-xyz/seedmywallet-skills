# Seed My Wallet API Reference

Quick reference for agents. Full OpenAPI spec: see `seedmywallet-openapi.json` in the main Seed My Wallet web app repo.

## Base URL

`https://preview-api.aerosol.so`

## Endpoints

### Check limits (pre-flight)

```
POST /test-data-seeder/check-limits
Content-Type: application/json

{
  "targetWalletAddress": "string",
  "tokenRequest": { "count": 0-20 },
  "emptyAccountRequest": { "count": 0-20 }
}

→ { "allowed": true } | { "allowed": false, "message": "..." }
```

### Create seed request

```
POST /test-data-seeder/order
Content-Type: application/json

{
  "targetWalletAddress": "string",
  "tokenRequest": { "count": 0-20 },
  "emptyAccountRequest": { "count": 0-20 }
}

→ 201: { id, status, targetWalletAddress, assetDelivery, createdAt, updatedAt }
→ 429: { message: "Rate limit exceeded: ..." }
```

At least one of tokenRequest or emptyAccountRequest must have count > 0.

## Rate Limits

- 50 tokens per hour (per wallet or IP)
- 50 empty accounts per hour (per wallet or IP)
