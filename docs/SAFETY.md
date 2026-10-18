# Safety

Monocurve's rules live in the on-chain program, not just the website. This page explains what each protection does
and, honestly, where its limits are.

## Sniper tax

Every buy in the first 15 minutes after launch pays a tax taken off the top of the buy:

| Time after launch | Tax |
|---|---:|
| Launch slot | 99% |
| 1 minute | 93.5% |
| 2 minutes | ~75% |
| 5 minutes | ~34% |
| 8 minutes | ~12% |
| 12 minutes | under 1% |
| 15 minutes and later | 0% |

- It falls a little **every slot** (about every 0.4 s). There's no cliff to wait out.
- During the first minute it stays above the most a coin can rise on the curve, so a snipe can't profit even if the
  coin graduates.
- **100% goes to the coin's holders** (its holder-rewards pot). Never to Monocurve, the insurance pool or the creator.
  The sniper-tax part of the pot stays locked for 24 h, and wallets that paid the tax are skipped when it's paid out,
  so a sniper can't flip and claw it back.
- **The creator pays it too.** The creator can't buy at all for the first 10 minutes; a creator buy between 10 and
  15 minutes pays the tax like anyone else's.
- Sells are never taxed. Sniper tax isn't part of your refund cost basis.

## Launch window

| Protection | What it does | Limit |
|---|---|---|
| **No same-transaction bundles** | No buy may share a transaction with the coin's launch. | — |
| **No dev snipe** | The creator can't buy for the first 10 minutes. | — |
| **One buy each, first 30 s** | Every wallet, or group of funding-linked wallets, gets exactly one buy. | Linking relies on funding history |
| **Established wallets, first 10 min** | Buyers need a signed check: wallet 3+ days old with 10+ transactions. | Old wallets and bots can still buy |
| **Per-slot cap, first 5 min** | At most 1% of supply can be bought per slot. | Spreading buys out is slower, not blocked |
| **Linked wallets share one cap** | Wallets funded by the same source share a single 2.5% cap for the first 10 min. | Exchange or multi-hop funding isn't always linked |
| **Creator vesting** | Tokens the creator buys sit in a program escrow and unlock over 24 h. | — |

## Always on

- **2.5% wallet cap.** Checked on every buy and on every wallet-to-wallet transfer while the coin is on the curve.
  It's per wallet, not per person: it makes bundling **costlier, not impossible**.
- **40 SOL per buy.** No single curve buy can spend more than 40 SOL.
- **Sandwich protection.** Every trade must carry a non-zero minimum output within 10% of the price at the start of
  the slot (the app defaults to 2%, 3% in the first 10 minutes, and lets you go up to 10%), and a deadline no more than
  ~60 s out. The same wallet can't buy and sell one coin in the same slot. The app sends trades through a private route first
  when one is available, and the **Sandwich protected** badge on each trade shows your limits. A bot can still take up to your
  slippage setting, so keep it tight.
- **One coin per ticker.** Tickers are normalised before registration: case, full-width and Cyrillic/Greek
  look-alikes, version suffixes (`2`, `V2`), common prefixes/suffixes (`BABY`, `INU`, `AI`…) and leetspeak all fold
  to one ticker. A ticker is freed again only if its coin fails without graduating, and then the wallet that launched
  it (and wallets linked to it) waits 30 days to use it again.
- **Anti-squat.** A 0.02 SOL launch deposit (back to the creator at graduation, kept by the platform if the coin
  fails) and at most 3 of a wallet's coins on the curve at once.
- **Thesis registered on-chain.** Each coin is backed by a written thesis; its hash is registered so the exact same
  text can back only one coin.
- **Anyone can sell, any time.** There is no sell cooldown.

## Signals

- **Serial-deployer flag.** Creator wallets are clustered by funding chains (up to 3 hops), direct transfers and shared
  early buyers. A cluster that launches 3+ coins in 24 h, or 2+ on the same narrative within 6 h, is flagged on every
  coin it made. Exchanges, faucets and public sniper bots are excluded so unrelated people aren't merged.
- **Original badge.** Among coins on the same narrative, the earliest un-flagged, non-rugged coin with real holders is
  marked *Original*; later lookalikes are marked *Newer copy*.
- **Verified handles.** The ✓ only means a creator proved an X handle with X sign-in **and** a wallet signature.
- **Bundle check & linked wallets.** Live holder-concentration and shared-funder signals on every coin, with linked
  wallets shown. A signal, not proof.

## Refund mode

A coin enters refund mode when either:

- **7 days pass without graduation.** Anyone can trigger it.
- **It's flagged as a rug.** The price fell 50%+ from a meaningful peak **and** either the creator dumped (sold at
  least half of what they bought, and at least 1% of supply) or the off-chain detector flagged it.

Then trading stops and holders burn tokens to redeem. Refunds can't be paused.

### How much you get back

Every wallet's **cost basis** is tracked on-chain: the part of each buy that actually stayed in the curve (trade fees
and sniper tax don't count). Selling reduces it proportionally.

When refunds open, the coin's escrowed creator fees are added to what holders can redeem, and the program takes a
snapshot:

```
B = total basis of all holders
P = min(SOL in the curve incl. escrowed creator fees, B)   ← what's available for basis holders
ratio = P / B                                               ← at most 1
```

Each holder receives:

```
refund = your_basis × ratio
       + share of leftover escrowed creator fees (pro rata to tokens)
       + share of the coin's holder pot (sniper tax and unpaid rewards)
       + insurance top-up (up to +10%)
       + rug slice (rug-flagged coins only)
```

- **Basis is paid in full unless the coin is short.** Only if early sellers took profit is everyone scaled to the
  same reduced ratio. Tokens that carry no basis share any leftover pro rata.
- Tokens bought or received in the 24 hours before refunds open (on a 7-day expiry) get no holder-pot or escrow share.
- **Claim order never matters.** Every share is a fixed fraction of the snapshot, and the vault can't be overdrawn.
- The creator and wallets linked to the creator get no top-up, escrow, pot or rug share.

### Insurance top-up: "up to 10%"

- A per-coin budget is reserved when refunds open: **10% of P, but never more than that coin itself paid into
  insurance.**
- It's paid pro rata, so first and last claimers get the same percentage.
- All insurance payouts together are limited to 10% of the pool per 24 h, so one coin can't drain it for others.
- Unclaimed budget returns to the pool after 30 days.
- Rug-flagged coins can also get a rug slice: the smaller of 1 SOL, 2% of the pool, or what the coin itself paid into insurance.

## What we don't promise

- Not "no bots" or "no bundles". These rules make them slower and costlier, and visible.
- Not "you always get 100% back". Refunds are pro rata to what's in the coin, plus a capped top-up while the pool lasts.
- TP/SL orders need a keeper to fire them; the fill price isn't guaranteed beyond your slippage. See [ORDERS.md](ORDERS.md).
- Monocurve has been live on Solana mainnet since October 16, 2026.
