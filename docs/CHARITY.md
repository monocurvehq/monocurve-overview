# Charity coins

When launching a coin, a creator can make it a **charity coin**.

- **100% of the creator fee** goes to the charity: the 0.20%, any community-boost uplift, the creator's share of
  locked-LP fees after graduation and the creator's half of any graduation bonus.
- **The creator gets none of it.** The program refuses creator fee claims on charity coins.
- It's **all or nothing**: the program accepts only "no charity" or "100% charity". The choice is fixed at launch.

## Held until the coin graduates

Like every creator fee, the charity's share is held in the coin's own vault while the coin is on the curve. Payouts to
the charity open only **after the coin graduates**. If the coin fails (refund mode), the held charity share goes to
that coin's holder refunds instead, not to the creator and not to Monocurve.

## Scam-proof by design

| Rule | Why |
|---|---|
| **Pick from a list, never type an address** | A coin stores a charity registry entry, never a wallet. Nobody can route "charity" fees to themselves. |
| **Verified registry** | Each charity's Solana wallet is checked against the charity's own official website. |
| **Re-checked before every payout** | The payout run re-verifies the official page first and stops if anything doesn't match. |
| **48-hour public notice** | Adding a charity or changing its wallet only takes effect 48 h after it's proposed, in public. |
| **Fail closed** | If a charity is paused or retired, its share stays in the coin's vault. It never goes to the creator or to Monocurve. |
| **Names are locked** | A charity's display name is fixed in the registry, so coins can't show look-alike names. |

## How payouts work

1. Each trade adds the creator slice to the coin's charity balance, held in the coin's own vault.
2. After the coin graduates, anyone can trigger a payout; the program sends it only to the registry's current verified
   wallet.
3. Every payout is an on-chain transaction. Totals and each payout's transaction link are public on the Fees page.
