# Chains and base assets

Pass `chain` to `token_quote`, `token_risk`, `swap_quote`, `swap_build` and `wallet_holdings`. A 0x address defaults to Robinhood Chain; a Solana address (base58) means Solana.

| chain | Name | Base assets by name | Notes |
| --- | --- | --- | --- |
| robinhood | Robinhood Chain (4663) | ETH, WETH, USDG | Home chain. Stock tokens can't be swapped yet. |
| base | Base (8453) | ETH, WETH, USDC | |
| ethereum | Ethereum (1) | ETH, WETH, USDC, USDT | Gas costs more; say so on small trades. |
| arbitrum | Arbitrum (42161) | ETH, WETH, USDC, USDT | |
| solana | Solana | SOL, USDC, USDT | Tokens by mint address. One versioned transaction per swap. |

Any other token: pass its contract address (0x...) or mint.

## Amounts

`amount` is in whole units of the token being sold: "100" USDG, "0.25" ETH. Not wei, not lamports.

## Fees

Every quote lists fees separately:

- Fivebar: 0.5%, paid inside the swap, taken from the ETH, WETH or stablecoin side (SOL side on Solana).
- The router (0x): 0.15% on some pairs.
- Network gas: paid by the user's wallet in ETH (SOL on Solana).

## Wallets

The user's own wallet signs and sends every transaction. Fivebar never holds keys or funds. Gas comes from the same wallet, so it needs a little ETH (or SOL) as well as the token it sells.
