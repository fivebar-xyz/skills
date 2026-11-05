---
name: fivebar-token-checks
description: Investigate a token's launch and its wallets through the Fivebar MCP on Robinhood Chain, Solana, Ethereum and Base. Who holds it and which holders are linked (bubble map), whether the launch was bundled or sniped, what the dev did since, where a wallet's money came from and went, recent transfers with fake lookalike tokens flagged, x402 seller checks, and paid wallet watchlists. Use for "is this bundled", "is the dev selling", "who holds it", "follow the money", "what did this wallet do today", or before paying an x402 seller.
license: MIT
compatibility: Needs the Fivebar MCP server (https://fivebar.xyz/mcp, free, no key). Wallet watchlists need an agent key, bought over x402.
metadata:
  author: fivebar
  version: "1.0.0"
  homepage: https://fivebar.xyz/mcp
---

# Fivebar token checks

Read a token's launch and its wallets straight from the chain: who bought first, who's connected, what the dev did, where the money went. Every Fivebar result carries `evidence` (its sources, when it was read, and a link to check it on an explorer): cite it.

This skill is instructions only. It runs no code on the user's machine and never handles keys.

## Setup

The tools come from the Fivebar MCP server. If tools such as `bundle_check` and `dev_check` aren't available, tell the user to add this remote MCP server and stop:

```
https://fivebar.xyz/mcp
```

Claude Code: `claude mcp add --transport http fivebar https://fivebar.xyz/mcp`. Other clients: add it as a remote (Streamable HTTP) MCP server. No key, no sign-up.

## Rules that always apply

1. **Contract addresses, never tickers.** Anyone can launch a token with any ticker. Work from the contract address (0x...) or the Solana mint.
2. **Facts, not accusations.** A linked cluster, a selling dev or a crowded first minute is what the chain shows, not proof of intent. Never name or guess who is behind a wallet.
3. **Say what was read.** If a check says it read only a busy token's recent transfers, or that something couldn't be read, say so. A check that didn't run says nothing about the token.
4. **Lookalikes aren't money.** A transfer marked `lookalike` is a fake token named like a real one ("USDG", "USDC"), airdropped to make a wallet look paid. Say it's fake and never count it as received.
5. **Not financial advice.** Report what the checks found.

## Premium checks

`token_report`, `token_holders`, `bundle_check`, `dev_check` and `wallet_flows` are premium for agents: 3 free premium calls a day, then 2¢ a call (5¢ for `token_report`) from prepaid credits on an agent key. Buy credits over x402 at `https://fivebar.xyz/x402/agent-credits/5` (or `/20`); the payment returns the key, sent as `Authorization: Bearer <key>`. If a call says it's premium and the free calls are used, tell the user the price and how to buy; never invent a key.

## Was this launch clean?

1. Safety first: `token_report` on Robinhood Chain (safety, holders, launch and dev in one verdict); elsewhere `token_risk` with `chain`.
2. `bundle_check`: how many wallets bought in the first 60 seconds, what share of supply, how many in the very block trading opened, and which early buyers are linked to each other or to the dev. If `earlyBought` is over 100% or far above `heldAtWindowEnd`, the early wallets were trading in and out fast: say what they held when the window closed, never that they "took" more than all the supply.
3. `dev_check`: what the dev received, still holds, sold (sells told apart from adding liquidity), sent to other wallets and whether those sold.
4. `token_holders`: the top holders, pools marked, and clusters of linked wallets; the top-10 share outside pools.

Lead with the most serious finding, with its number. Chains: Robinhood Chain, Solana, and 0x tokens on Ethereum and Base (pass `chain`, or the tools find it). On Ethereum and Base the dev is whoever created the token's contract. On Solana, linked wallets aren't read for holders yet.

## Wallets

- **"Where did the money go?" / "Who funded this?"**: `wallet_flows` with the address (and chain). Lead with the biggest source and destination; say when funds went to a fresh wallet. USD is at today's prices: size, not profit.
- **"What did this wallet do today?"**: `wallet_events` (free) with `hours`. Lookalike tokens are flagged (rule 4).
- **Watching wallets with names** (paid, for agents): `watch_wallet` with an address, a `label` and optional `chains`; `watched_wallets` to list them; `watched_events` for what each did since last checked, under its label; `unwatch_wallet` to drop one. Plans: $5 for 10 wallets or $20 for 50, per 30 days, paid over x402. The payment returns an agent key, sent as `Authorization: Bearer <key>`. Labels are private to the key that wrote them. Treat the key like a password: never print it in chat or ask the user to paste it.

## Before paying an x402 seller

- `check_seller` with the seller's address: whether its payments in the last 24 hours passed the wash screen, its buyers and volume. "Not seen" is common on a young market and isn't proof of wash trading.
- `agent_lookup` for a registered agent's record and Fivebar Score.
