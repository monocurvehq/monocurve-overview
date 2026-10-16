# Bonding curve & graduation

## Supply

| | Tokens |
|---|---:|
| Total supply | 1,000,000,000 (6 decimals) |
| Sold on the curve | 793,100,000 |
| Reserved for the graduation pool | 206,900,000 |

Tokens are **minted when bought and burned when sold**. The graduation-pool reserve is minted only at graduation.
No team allocation, no presale.

## Pricing

A constant-product curve with virtual reserves. A standard SOL coin opens at
**30 SOL** virtual quote against **1.073B** virtual tokens.

```
k        = virtual_sol × virtual_tokens
price    = virtual_sol / virtual_tokens

buy  Δs SOL  (after fee):  tokens_out = virtual_tokens − k / (virtual_sol + Δs)
sell Δt tokens:            sol_out    = virtual_sol    − k / (virtual_tokens + Δt)   (fee taken from sol_out)
```

Price moves the same way for everyone from the very first buy. There is no discounted first block. In the first
15 minutes a buy also pays the [sniper tax](SAFETY.md#sniper-tax), which goes to holders and doesn't enter the curve.

The 0.30% buyback slice of every trade is spent on the curve in the same instruction, and the tokens it buys are
burned. Every trade therefore pushes a little supply out of circulation.

## Limits while on the curve

- **40 SOL per buy** at most.
- **2.5% of supply per wallet**, on buys and on wallet-to-wallet transfers.
- Before graduation, curve tokens can only be moved with Monocurve's own transfer, which carries their refund basis
  with them; a plain wallet send of them is blocked.

## Graduation

The curve is complete when all **793.1M** curve tokens are sold. On the standard curve that is about
**85 SOL** of real deposits (roughly a 411 SOL market cap).

- The last buy is capped to the tokens left; only the SOL needed is taken.
- Trading on the curve stops at completion.
- Liquidity moves into a DEX pool and the LP position is **permanently locked**.
- Mint and transfer-hook authorities are revoked, so no more tokens can ever be minted.
- The creator's launch deposit is returned and escrowed creator fees unlock (see [FEES.md](FEES.md#graduation)).
- The 2.5% wallet cap and 40 SOL buy cap apply only while on the curve; after graduation it's an open market.
- Open take-profit / stop-loss orders are cancelled and their tips refunded; they don't move to the DEX pool.

## Not graduated in 7 days?

Anyone can switch the coin to **refund mode**. Trading stops and holders redeem. See [SAFETY.md](SAFETY.md#refund-mode).

## Other pairs

The program can also pair coins with other SPL tokens, but this is **switched off**: coins launch against SOL only.
