# Seed My Wallet Skills

AI agent skills for using [Seed My Wallet](https://seedmywallet.com)—the free tool that populates Solana devnet wallets with test SPL tokens and empty token accounts in seconds. No signup, no API keys.

## What are Skills?

Skills are markdown files that give AI agents specialized knowledge for specific tasks. When you add these to your project, Claude Code, Cursor, or similar agents can recognize when you're working with Solana devnet and help you seed wallets correctly.

## Skill

**[seed-my-wallet](skills/seed-my-wallet/)** — Use Seed My Wallet: CLI workflow (solana config, airdrop, seed via API), web app, API, curl, check-limits, integration examples, troubleshooting.

## Installation

### Option 1: Cursor (npx skills)

```bash
npx skills add aerosol-xyz/seedmywallet-skills
```

### Option 2: Clone and copy

```bash
git clone https://github.com/aerosol-xyz/seedmywallet-skills.git
cp -r seedmywallet-skills/skills/* .cursor/skills/
```

### Option 3: Claude Code plugin (if supported)

```bash
/plugin marketplace add aerosol-xyz/seedmywallet-skills
/plugin install seedmywallet-skills
```

## Usage

Once installed, ask your agent:

- "Seed my devnet wallet 7dzNv3is8js2faFrcLdrFJw23n3cGuzNYsL4ynYm1ikX with 5 tokens and 3 empty accounts"
- "How do I use Seed My Wallet to populate a wallet for testing?"
- "I need test tokens on devnet—what's the easiest way?"
- "Add a script that seeds a wallet via the Seed My Wallet API"

## Quick Reference

| Item | Value |
|------|-------|
| Web app | [seedmywallet.com](https://seedmywallet.com) |
| API | `POST https://preview-api.aerosol.so/test-data-seeder/order` |
| Check limits | `POST https://preview-api.aerosol.so/test-data-seeder/check-limits` |
| Rate limits | 50 tokens/hour, 50 empty accounts/hour |

## License

MIT

Made with ❤️ by [Aerosol](https://aerosol.so)
