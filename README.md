# EuroRWA — Research Project

> Independent research into **public data transparency and verifiable snapshots** for tokenized money-market funds (MMFs).

This repository is a **research project**. It studies how publicly available tokenized-MMF data can be collected, stored, and independently verified. It is provided as code and methodology for studying open financial data — not a product, service, or offering, and no hosted service, dashboard, or bot is operated here.

**Free and open.** The code is MIT-licensed and free to fork and use. There is **no paywall and no advertising** — every endpoint (REST + MCP) is open and the whole verification path runs with no credentials. The one exception is spelled out under [Status](#status): a Binance account key for signed calls, and a signing key to attest on-chain. Monetization and freelance/lead-generation features that previously existed here have been removed.

## Research questions

- How can a daily fund snapshot be made **independently verifiable** (keccak-256 hash → signed → published on-chain)?
- What does public tokenized-MMF data look like across EU + US issuers (TVL, yield, flows, holders)?
- Can an analyst layer produce **reproducible, explainable** signals from that data?

## What the code does

1. **Collect** public fund data (rwa.xyz) → SQLite snapshots.
2. **Attest** each snapshot: hash it (keccak-256), sign with a dedicated key, and publish the signature to a Sepolia contract.
3. **Analyze**: deterministic scoring plus an analyst layer over news / on-chain flows / macro context.
4. **Verify**: anyone can re-hash the data and check the on-chain signature.

## Components

| Component            | Path            |
| -------------------- | --------------- |
| Snapshot collector   | `src/fetch.ts`  |
| Store (SQLite)       | `src/ingest.ts` |
| Analyst              | `src/analyst/`  |
| On-chain attestation | `src/agent/`    |
| API (REST + MCP)     | `api/`          |
| Pipeline scripts     | `scripts/`      |

## Verify an attestation

```bash
bun run verify            # latest snapshot
bun run verify -- 2026-08-06   # a specific date
```

Verifies the payload hash matches the published keccak-256 and that the recovered signer is the agent wallet.

## Status

Research code, provided as-is for study. No hosted service is operated.

What needs credentials, stated plainly rather than as a blanket "no keys":

| Path | Needs |
|---|---|
| `bun run verify`, the read-only REST endpoints, the MCP server | nothing |
| `scripts/binance.js` signed account calls | `BINANCE_API_KEY`, `BINANCE_API_SECRET` — it throws rather than falling back to a public endpoint |
| attesting and publishing on-chain | a signing key for the agent wallet |
| the Telegram webhook at `api/_tgbot.js` | mounted on every request, but inert without `TG_TOKEN`; it answers `ok` and never calls Telegram |

So a stranger can clone this and run the whole verification path with no
credentials at all. The credentialed paths are the ones that touch an account.

## Compliance

Per Vietnamese law (prohibition on crypto/coin advertising and registration, effective 1 Sep 2026), this repository contains **no public web dashboard and no advertising**. It is distributed solely as open research code.

MIT © 2026 — data sourced from rwa.xyz public APIs. Not financial advice.
