# Fivebar skills

Skills that teach AI agents to work with live data and act on it safely, through the [Fivebar MCP](https://fivebar.xyz/mcp). Every answer shows its sources.

| Skill | What it teaches |
| --- | --- |
| [fivebar-trading](skills/fivebar-trading/SKILL.md) | Trade a token safely: find by address, check, quote with every fee, swap with your own wallet, watch. |
| [fivebar-token-checks](skills/fivebar-token-checks/SKILL.md) | Read a launch from the chain: bundles, the dev, holders and linked wallets, follow the money, fake lookalike tokens, x402 seller checks, wallet watchlists. Robinhood Chain, Solana, Ethereum and Base. |
| [fivebar-research](skills/fivebar-research/SKILL.md) | Due diligence from SEC filings: financials, insider trades, dilution, what a filing says, fund holdings; plus Polymarket odds. |

## fivebar-trading

The routine a careful trader follows, as one tool call per step:

1. **Find**: tokens by contract address only, never by ticker. Confirm name, decimals and price.
2. **Check**: `token_risk` before every buy. Honeypots, mint and freeze authorities, taxes, owners, thin liquidity.
3. **Size**: `swap_quote` with the minimum out and every fee shown, before anything is built.
4. **Swap**: `swap_build` returns unsigned transactions. Exact-amount approvals only. Your own wallet signs.
5. **Watch**: `wallet_holdings` after the swap, valued at what selling would get now. Airdrops nobody can sell are flagged, not counted.

## fivebar-token-checks

Was the launch clean? Safety, who bought in the first minute and whether they're linked, what the dev did since, who holds it now: four checks, run side by side. Then follow a wallet's money, read what it did today with fake lookalike tokens flagged, and check an x402 seller before paying. Facts, not accusations: wallets are never named.

## fivebar-research

Due diligence the way an analyst does it: growth and margins from the 10-K and 10-Q, insiders' open-market buys and sales (routine pay and 10b5-1 plans told apart), dilution (share growth first, then shelves, ATMs and warrants; splits never counted), the filing's own words, and a fund's 13F holdings. Every number comes with its filing and date.

**Instructions only.** No scripts, nothing runs on your machine, no keys ever asked for or handled. Each skill reads in a minute.

## Install

**Claude Code**: the plugin installs the skill and connects the Fivebar MCP in one go.

```
/plugin marketplace add fivebar-xyz/skills
/plugin install fivebar@fivebar
```

**Codex, Cursor, GitHub Copilot, Gemini CLI and other agents** that read Agent Skills:

```
npx skills add fivebar-xyz/skills
```

From a script or another agent (no prompts, every agent on the machine):

```
npx -y skills add fivebar-xyz/skills -y -g --agent '*'
```

Then connect the MCP if your agent doesn't have it yet: add `https://fivebar.xyz/mcp` as a remote (Streamable HTTP) MCP server. Free, no key.

**Any agent**: the MCP sends a short version of these routines when it connects, and offers seven prompts: "Trade a token safely", "Is this token safe?", "What do I hold?", "Was this launch clean?", "Due diligence on a company", "Check a seller before paying" and "Watch a wallet".

## Fees

Data tools are free. Wallet watchlists are a paid feature for agents: $5 for 10 wallets or $20 for 50, per 30 days, over x402. Swaps built through Fivebar carry a 0.5% fee, paid inside the swap and shown in every quote next to the router's fee and gas.

Not financial advice. [fivebar.xyz](https://fivebar.xyz) · [Privacy](https://fivebar.xyz/privacy)
