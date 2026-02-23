# AGENTS.md

Guidelines for AI agents working in this repository.

## Repository Overview

This repository contains **Agent Skills** for using [Seed My Wallet](https://seedmywallet.com)—a free tool that populates Solana devnet wallets with test SPL tokens and empty token accounts. Skills follow the [Agent Skills specification](https://agentskills.io/specification.md).

- **Name**: Seed My Wallet Skills
- **Product**: [Seed My Wallet](https://seedmywallet.com)
- **License**: MIT

## Repository Structure

```
seedmywallet-skills/
├── .claude-plugin/
│   └── marketplace.json   # Claude Code plugin marketplace manifest
├── skills/
│   └── seed-my-wallet/     # Single skill: CLI, API, web app, integration
│       └── SKILL.md
├── AGENTS.md
├── README.md
└── LICENSE
```

## Skill

**seed-my-wallet** — Use Seed My Wallet: CLI workflow (solana config, airdrop, seed via API), web app, API, curl, check-limits, integration examples, troubleshooting.

## API Reference

- **Base URL**: `https://preview-api.aerosol.so`
- **Endpoints**: `POST /test-data-seeder/check-limits`, `POST /test-data-seeder/order`
- **Rate limits**: 50 tokens/hour, 50 empty accounts/hour (per wallet or IP)

## Build / Lint

Skills are content-only. No build step. Validate:
- YAML frontmatter is valid
- `name` matches directory name
- `description` includes trigger terms
