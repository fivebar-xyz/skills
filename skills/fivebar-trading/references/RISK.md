# Reading token_risk

`token_risk` returns a `verdict` (high, caution or low) and a list of `flags`, each with a `level` (high, caution or info), a `check` and a plain-words `detail`. The verdict is the worst flag's level. Explain flags to the user in plain words; the `detail` text is written for that.

## Flags on every chain

| check | What it means | Level |
| --- | --- | --- |
| sell | A test bought $100 of the token but found no route to sell it back. Possible honeypot: you can buy but not sell. | high |
| liquidity | Cost of buying then selling $100: over 30% is high (very thin pool or hidden fees), over 8% is caution (keep size small). No route to buy $100 at all is caution. | high, caution or info |
| known | A major asset (ETH, WETH, a stablecoin, SOL). Nothing to check. | info |
| not checked | A check couldn't run right now (a data source was down). Say which; don't treat it as passed. | info |

## EVM chains (Robinhood Chain, Base, Ethereum, Arbitrum)

| check | What it means | Level |
| --- | --- | --- |
| buy tax / sell tax | The token takes a cut on buys or sells. 10% or more is high: hard to exit at a profit. | caution or high |
| owner | The contract still has an owner who may be able to change fees, limits or trading rules. "Ownership renounced" is info. | caution |

## Solana

| check | What it means | Level |
| --- | --- | --- |
| mint authority | Someone can still mint more tokens and dilute holders. Revoked is info. | high |
| freeze authority | Someone can freeze holders' tokens so they can't sell. Revoked is info. | high |
| permanent delegate | Someone can move or burn tokens from any holder's account (Token-2022). | high |
| transfer fee | Every transfer pays a fee to the token's owner (Token-2022). 10% or more is high. | caution or high |
| transfer hook | Every transfer runs another program that can block or change it (Token-2022). | caution |
| non-transferable | The token can't be transferred at all. | high |
| frozen by default | New holders' accounts start frozen. | high |
| pausable | Someone can pause all transfers. | caution |
| holders | Share of supply held by the 10 largest accounts (pools included). Over 50% is caution. | caution or info |

## How to act on the verdict

- **high**: stop and explain. Continue only if the user insists after hearing the risk, and say they could lose everything they put in.
- **caution**: explain each caution flag and ask before continuing. Suggest a small size.
- **low**: no known trap was found. That isn't a guarantee: new tokens can still go to zero.

The verdict is a snapshot. Owners, authorities and liquidity can change after the check, so check again before each buy.
