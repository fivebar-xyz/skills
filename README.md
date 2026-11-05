# Fivebar skills

Skills that teach AI agents to trade safely with the [Fivebar MCP](https://fivebar.xyz/mcp): on Robinhood Chain, Solana, Base, Ethereum and Arbitrum.

## fivebar-trading

The routine a careful trader follows, as one tool call per step:

1. **Find**: tokens by contract address only, never by ticker. Confirm name, decimals and price.
2. **Check**: `token_risk` before every buy. Honeypots, mint and freeze authorities, taxes, owners, thin liquidity.
3. **Size**: `swap_quote` with the minimum out and every fee shown, before anything is built.
4. **Swap**: `swap_build` returns unsigned transactions. Exact-amount approvals only. Your own wallet signs.
5. **Watch**: `wallet_holdings` after the swap, valued at what selling would get now. Airdrops nobody can sell are flagged, not counted.

**Instructions only.** No scripts, nothing runs on your machine, no keys ever asked for or handled. Read the whole skill in a minute: [SKILL.md](skills/fivebar-trading/SKILL.md).

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

Then connect the MCP if your agent doesn't have it yet: add `https://fivebar.xyz/mcp` as a remote (Streamable HTTP) MCP server. Free, no key.

**Any agent**: the MCP sends a short version of this routine when it connects, and offers three prompts: "Trade a token safely", "Is this token safe?" and "What do I hold?".

## Fees

Data tools are free. Swaps built through Fivebar carry a 0.5% fee, paid inside the swap and shown in every quote next to the router's fee and gas.

Not financial advice. [fivebar.xyz](https://fivebar.xyz)
