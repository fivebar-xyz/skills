---
name: fivebar-research
description: Research US-listed companies and funds from SEC filings through the Fivebar MCP, with every number tied to its filing. Financials, insider trades, dilution (share growth, shelves, ATMs, warrants, splits), what a filing says, who mentions a topic across every filing, and a fund's 13F holdings; plus Polymarket odds and Robinhood stock token prices. Use for due diligence on a stock, "is X profitable", "are insiders selling", "is X diluting", "what does Berkshire own", "what did the 10-K say about Y", or "what are the odds of Z".
license: MIT
compatibility: Needs the Fivebar MCP server (https://fivebar.xyz/mcp, free, no key).
metadata:
  author: fivebar
  version: "1.0.0"
  homepage: https://fivebar.xyz/mcp
---

# Fivebar research

Do the homework an analyst does before forming a view on a company: read the numbers, read what insiders did with their own money, read what the filings say, and check whether shareholders are being diluted. Every Fivebar result carries `evidence` (its sources, when it was read, how fresh the data is, and a link): cite it.

This skill is instructions only. It runs no code on the user's machine.

## Setup

The tools come from the Fivebar MCP server. If tools such as `company_financials` and `insider_trades` aren't available, tell the user to add this remote MCP server and stop:

```
https://fivebar.xyz/mcp
```

Claude Code: `claude mcp add --transport http fivebar https://fivebar.xyz/mcp`. Other clients: add it as a remote (Streamable HTTP) MCP server. No key, no sign-up.

## Rules that always apply

1. **Every number from a filing.** Say the period and the filing date with each figure ("fiscal 2026, 10-K filed Nov 3"). Never fill a gap from memory; if a tool has no figure, say so.
2. **Routine pay isn't selling.** Only open-market buys and sales are insiders' choices. Grants, option exercises and shares withheld for tax are routine pay. A sale under a 10b5-1 plan was scheduled in advance: say so.
3. **A shelf or ATM isn't a sale.** It allows selling shares; it doesn't prove any were sold. Dilution that already happened is share growth. S-8 is routine employee stock.
4. **Never call a split dilution.** If share counts jump at a split or reverse split, say so and don't quote growth across it.
5. **People as the filing names them.** Officers, directors and fund managers may be named exactly as the filing names them, with their role, for what the filing shows. No guesses about motive.
6. **Not financial advice.** Report what the filings show. Don't tell the user to buy or sell.

## Premium checks

`stock_report` (10¢) and `dilution_check` (5¢) are premium for agents: 3 free premium calls a day, then paid from prepaid credits on an agent key bought over x402 at `https://fivebar.xyz/x402/agent-credits/5` (or `/20`), sent as `Authorization: Bearer <key>`. The other research tools are free.

## Due diligence on a company

Start with `stock_report`: the trend over eight quarters (revenue against the same quarter a year before, margins, free cash flow, inventory or receivables outgrowing sales), valuation from the Robinhood stock token when there is one (computed: say so), management's outlook quoted from the latest earnings release, the 8-Ks of the last 120 days, dated catalysts ahead (the next earnings date is estimated from last year's: say so), and what to watch (risk factors new in the latest 10-K, customer concentration, going-concern language). Then, as needed:

1. `company_financials` (period `quarterly` for recent quarters, `annual` for years): revenue and its growth, margins, cash against debt, share count. Lead with growth and profitability.
2. `insider_trades`: open-market buys and sales over the window, who (as named, with role), how much, and whether under a 10b5-1 plan.
3. `dilution_check`: share growth over 1 and 3 years first, then what's open (shelf, ATM, recent offerings, warrants). `offeringTerms` reads the latest 424B5 prospectus supplements themselves: an ATM's maximum, shares offered and price, warrants and their exercise price, and how much one offering dilutes when the share count is stated. Quote the sentence each term came from, with the form and date; a term that isn't there wasn't stated, so don't guess it. Shares up 20%+ in a year, or an ATM with recent sales, deserves a clear warning.
4. `filing_text` for anything the numbers can't say: risk factors, management's discussion, legal proceedings, what an 8-K announced. Ask for a section by name, or pass `query` for the passages. Quote short, and name the form and date.

Then answer as one story, ending with what's ahead (the nearest dated catalyst) and what to watch: what the numbers show, what insiders did, whether holders are being diluted, and anything in the filings that changes the picture. Say where the sources agree or pull apart ("revenue up 12%, yet insiders sold $40M").

## Other questions

- **"What does <fund> own?" / "What did they buy?"**: `fund_holdings` with the manager's legal name. 13F holdings are as of quarter end, filed up to 45 days later, long US positions only: say so.
- **"Who mentions X?" / "Which companies disclosed Y?"**: `filing_search`, then `filing_text` on the filings that matter before saying what they say.
- **A stock's price on-chain**: `stock_quote` for the Robinhood stock token (Chainlink price, per token, with its multiplier). Its business is `company_financials`, not the token.
- **"What are the odds of...?"**: `prediction_markets` with short, neutral topic words ("Fed December", not the user's whole question), or `prediction_market` for one market by link. Quote the market that asks the question most directly, usually the one with the most volume; name it; give the weekly change; say when it's thin. `closes` is when the market closes, not when the event happens. Market prices aren't forecasts; never suggest a bet.

## Limits

- Only companies that file with the SEC. Private companies and most non-US companies don't: say that rather than guessing.
- Funds and trusts may have no tagged financials; their filings are still listed.
- Figures are as reported, in USD (not thousands), EPS per share.
