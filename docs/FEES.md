# Fees

**Every bonding-curve trade pays 0.90%**, on buys and sells alike.

| Slice | Share of the trade | Goes to |
|---|---:|---|
| Creator | **0.20%** | The coin's creator (or, on a charity coin, the charity). Held in escrow until the coin graduates. |
| Platform | **0.25%** | Monocurve |
| Buyback & burn | **0.30%** | Buys the coin on its own curve and burns it, in the same trade |
| Insurance | **0.15%** | The insurance pool that tops up refunds |
| **Total** | **0.90%** | |

Rounding dust always goes to buyback & burn, so the four slices add up to exactly the fee charged.

## Launching

- **0.02 SOL launch deposit.** It's held in the coin's own escrow account, not by Monocurve. It goes back to the creator
  when the coin graduates. If the coin fails (refund mode or a rug flag), the deposit goes to the platform treasury, so
  mass-squatting tickers isn't free. The program caps this deposit at 1 SOL; it can't be set higher.
- **Up to 3 active coins.** A wallet can have at most 3 coins on the curve at once. A slot frees up when a coin graduates
  or fails.
- **Ticker cooldown.** When a coin fails, its ticker is freed, but the wallet that launched it (and wallets linked to it)
  must wait 30 days before launching that ticker or a look-alike again.
- Plus normal Solana network rent.

## Creator fee escrow

While a coin is on the curve, the creator slice (or the charity slice on a charity coin) stays locked in the coin's
vault. It can only be claimed **after the coin graduates**.

If the coin fails instead (7 days without graduating, or a rug flag), the escrowed creator fees are added to that
coin's **holder refunds**: they first cover any shortfall in holders' cost basis, and the rest is shared across every
refunded token. The creator and wallets linked to the creator get no share of it.

## Community fee boost

A coin that keeps many **real** holders for weeks moves part of its buyback slice to the creator side.
Traders still pay exactly 0.90%. Platform, insurance and referral shares never change.

| Tier | Real holders kept | Creator | Buyback & burn |
|---|---|---:|---:|
| 0 | — | 0.20% | 0.30% |
| 1 | 100+ for 7 days | 0.23% | 0.27% |
| 2 | 250+ for 14 days | 0.26% | 0.24% |
| 3 | 500+ for 30 days | 0.30% | 0.20% |

A *real holder* holds at least $5, from an aged wallet, outside the creator's funding cluster, with one count per
bundle cluster. Tiers are signed off-chain, bound to one coin, and lapse after 3 days unless re-confirmed, so a coin that
loses its holders drops back to tier 0. Rugged or refunding coins always pay tier 0.

## Platform slice and the insurance pool

Of the 0.25% platform slice:

1. **Referrals are paid first** (never charged extra to the buyer): 0.08% of the trade to a scout whose on-chain call
   referred the buy, or 0.10% to an approved streamer. The platform always keeps at least 60% of its slice.
2. **30% of what's left feeds the insurance pool**, until the pool holds **500 SOL**.
3. The rest is platform revenue.

The insurance pool also receives the dedicated 0.15% insurance slice. Once the pool is at its 500 SOL cap, both the
pool's share of the platform slice and the 0.15% insurance slice go to platform revenue instead, until the pool drops
below the cap again.

**The pool has no admin withdraw path.** It only pays out for refund top-ups, rug refunds and graduation bonuses,
through a limiter that releases at most 10% of the pool per 24 hours.

## Graduation

- A flat **0.015 SOL** graduation fee to the platform.
- The creator's 0.02 SOL launch deposit is returned, and escrowed creator fees unlock.
- **Scout callouts:** if anyone registered an on-chain call on the coin, 15% of its escrowed creator fees go to callers
  at graduation: 10% shared by how early they called, plus a 5% bounty to the first caller.
- **Graduation bonus:** once per coin, the insurance pool pays the smaller of 1% of the pool, 0.25 SOL, or what the coin
  itself paid in fees to the platform and insurance. Half goes to the creator (or charity), half to the first caller
  (all to the creator if nobody called it).

### Fees after graduation (bonding)

After graduation the coin trades in a DEX pool with a **0.90% pool trading fee**. The pool's liquidity position is
permanently locked, and **it earns that trading fee**. Anyone can claim the locked position's fees, and they are split
exactly like curve fees:

| Slice | Share of pool volume (before the DEX's own cut) |
|---|---:|
| Creator (or charity) | 0.20% (up to 0.30% with the community boost) |
| Platform | 0.25% |
| Buyback & burn | 0.30% (down to 0.20% with the boost) |
| Insurance | 0.15% |

So **Monocurve keeps earning about 0.25% of volume after a coin bonds**; the rest goes to the creator, buyback & burn
and the insurance pool. The DEX takes its own protocol share of the pool fee first, so the actual amounts are slightly
lower. Buyback-and-burn after graduation buys on the DEX pool.

## Example: a 1 SOL buy (after the 15-minute launch window)

| | SOL |
|---|---:|
| Fee (0.90%) | 0.0090 |
| → Creator (escrowed until graduation) | 0.0020 |
| → Platform | 0.0025 |
| → Buyback & burn | 0.0030 |
| → Insurance | 0.0015 |
| Goes into the curve | 0.9910 |

During the first 15 minutes the sniper tax is taken off the top first and goes to the coin's holders; the 0.90% fee is
charged on what's left. See [SAFETY.md](SAFETY.md#sniper-tax).

## Who earns what, in one place

| Who | From |
|---|---|
| **Monocurve** | 0.25% platform slice of curve trades (after referrals and the insurance-pool share), the 0.015 SOL graduation fee, launch deposits of coins that fail, insurance inflows once the pool is full, and about 0.25% of DEX-pool volume after graduation (its slice of the locked liquidity's fees) |
| **Creator** | 0.20% (up to 0.30%) creator slice, claimable only after graduation (minus any scout callout share); nothing on a charity coin |
| **Holders / scouts** | All sniper tax; callout shares for early on-chain callers; escrowed creator fees and refunds if a coin fails; reward-coin payouts |
| **Everyone** | 0.30% buyback & burn reduces supply on every trade |
