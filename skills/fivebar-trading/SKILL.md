---
name: fivebar-trading
description: Trade tokens safely through the Fivebar MCP on Robinhood Chain, Solana, Base, Ethereum and Arbitrum. Finds a token by contract address, checks it for rugs, honeypots and taxes before buying, quotes with every fee shown, builds unsigned swaps for the user's own wallet, and checks holdings after. Use when the user wants to buy, sell or swap a token or memecoin, asks if a token is safe, wants a price or a quote, or asks what a wallet holds.
license: MIT
compatibility: Needs the Fivebar MCP server (https://fivebar.xyz/mcp, free, no key) and a wallet the user controls to sign swaps.
metadata:
  author: fivebar
  version: "1.0.0"
  homepage: https://fivebar.xyz/mcp
---

# Fivebar trading

Help the user trade like a careful trader: find the right token, check it, size the trade, swap with their own wallet, then confirm what they hold. Every step is one Fivebar tool call.

This skill is instructions only. It runs no code on the user's machine and never handles keys.

## Setup

The tools come from the Fivebar MCP server. If tools such as `token_risk` and `swap_quote` aren't available, tell the user to add this remote MCP server and stop:

```
https://fivebar.xyz/mcp
```

Claude Code: `claude mcp add --transport http fivebar https://fivebar.xyz/mcp`. Other clients: add it as a remote (Streamable HTTP) MCP server. No key, no sign-up.

## Rules that always apply

1. **Contract addresses, never tickers.** Anyone can launch a token with any ticker. Only the chain's base assets go by name (ETH, WETH, USDG on Robinhood Chain; ETH, WETH, USDC, USDT on Base, Ethereum and Arbitrum; SOL, USDC, USDT on Solana). For anything else, get the address from the user or from a source they trust. If they give only a ticker, ask for the address; don't guess one.
2. **Check before every buy.** Run `token_risk` on the token being bought. Never skip it, even for a token bought before: owners and liquidity change.
3. **Quote before you build.** Show the `swap_quote` result before calling `swap_build`.
4. **The user signs.** Fivebar returns unsigned transactions. Never ask for, accept, store or repeat a private key or seed phrase. If the user pastes one, tell them to move their funds to a new wallet.
5. **Exact approvals only.** An approval step must be for the exact sell amount, to the router contract the build named. Never sign an unlimited approval or an approval to any other address.
6. **The user decides.** Get a clear yes before every swap unless the user has set a standing rule (for example "swaps under $25 without asking"). Never go above the size they set.
7. **Not financial advice.** Report what the tools show. Don't predict prices or tell the user what to buy.

## The routine

### 1. Find

- Pin down the chain and the contract address. A 0x address defaults to Robinhood Chain; pass `chain` for Base, Ethereum or Arbitrum. A Solana address is a mint.
- `token_quote` with the address (and `chain`): confirm the name, symbol and decimals match what the user expects, and note the price and market cap.
- If the result says the pool is thin (`liquidity: "thin"`, `priceSource: "router"`), tell the user: the pool's own price is unreliable, and big trades will move the price.

### 2. Check

- `token_risk` with the address (and `chain`). Read the verdict:
  - **high**: stop. Tell the user what was found (for example "the $100 buy-then-sell test failed: you can buy but not sell", "a 25% transfer fee", "someone can still mint more"). Only continue if they insist after hearing it, and say again that they could lose the whole amount.
  - **caution**: explain each flag in plain words and ask whether to continue.
  - **low**: say so briefly and continue. Low means no known trap was found, not that the token is safe.
- The round-trip test shows what buying and then selling $100 costs. Above 8% is flagged caution (expensive exits); above 30%, or no route back at all, is high.
- See [references/RISK.md](references/RISK.md) for what each flag means.

### 3. Size

- `swap_quote` with `sell`, `buy`, `amount` (in whole units of the sell token), `chain`, and `taker` (the user's wallet, so it can warn about a missing balance or approval).
- Show the user, in this order:
  - what they give and the minimum they get after slippage;
  - every fee on its own line: Fivebar's 0.5%, the router's fee if any, and network gas;
  - price impact, if large, and any warnings (taxes, low balance, approval needed).
- Default slippage is 1%. Raise it only if the user asks, and never above 5% without warning that a bad fill gets more likely.
- If the trade is large next to the token's liquidity, suggest a smaller amount first.

### 4. Swap

- After the user says yes: `swap_build` with the same arguments, including `taker`.
- EVM chains return steps in order: an approval (only when needed), then the swap. Check the approval is exact (rule 5) and the swap goes to the contract the build named. The user's wallet signs and sends each step, in order, waiting for each to confirm.
- Solana returns one versioned transaction (base64) for the user's wallet to sign and send.
- If the quote has moved since the user said yes (a worse minimum, new warnings), show the new numbers and ask again.

### 5. Watch

- After the swap confirms: `wallet_holdings` for the user's wallet on that chain. Confirm the tokens arrived and report the position.
- Holdings are valued at what selling would get now (`valueBasis: "sell quote"`), which can be below the quoted price for thin tokens. Say so when `paperValueUsd` is higher.
- Tokens under `unpriced` with "can't be sold" are often unsolicited airdrops. Tell the user not to approve, sell or interact with them: some exist to trick wallets into signing something harmful.

## Other things the user may ask

- **"Is this token safe?"**: steps 1 and 2 only, and report. No quote, no build.
- **"What do I hold?"**: `wallet_holdings` with their address (all four EVM chains for a 0x address, or Solana). Lead with the total, flag anything flagged by its risk verdict, list the airdrops to leave alone.
- **Robinhood stock tokens**: `stocks_list` and `stock_quote` (Chainlink prices). Stock tokens can't be swapped through Fivebar yet ("xStocks coming soon").
- **Perps**: `perps_markets` and `perp_quote` for Hyperliquid data (read only, no trading).
- **Paying an x402 seller or trusting another agent**: `check_seller` (is its demand real?) and `agent_lookup` (its Fivebar Score). "Not scored yet" means too little independent evidence, not a bad record.

## When something goes wrong

- **"needs its contract address"**: the user gave a ticker. Ask for the address.
- **"No token at that address"**: wrong chain or a typo. Ask which chain, or check the address.
- **"xStocks coming soon"**: stock tokens can't be swapped yet; offer `stock_quote` for the price.
- **"This wallet can't swap through Fivebar"**: the wallet is on the US sanctions list. Don't retry with another route or wallet.
- **Rate limit**: 60 calls a minute. Wait and retry once.
- **No route / no liquidity**: the token can't be traded at that size right now. Try a smaller amount or stop.

See [references/CHAINS.md](references/CHAINS.md) for chain names and base assets.
