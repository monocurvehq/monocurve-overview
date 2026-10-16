# Fair refunds

Each wallet's `basis` is the SOL it actually put into the curve (net of fees and sniper tax). Buys add to it; sells
remove it in proportion to the tokens sold.

```rust
fn md(a: u64, b: u64, c: u64) -> u64 { if c == 0 { 0 } else { (a as u128 * b as u128 / c as u128) as u64 } }

/// creator fees (or a charity's share) are escrowed while the coin is on the curve. If the coin fails,
/// they are folded into what holders can redeem, right before the snapshot.
fn fold_creator_escrow(c: &mut Curve) -> u64 {
    let e = c.creator_fees_accrued + c.charity_accrued;
    c.raised += e;
    c.creator_fees_accrued = 0;
    c.charity_accrued = 0;
    e
}

/// once, when refunds open. `escrow` = what fold_creator_escrow added.
fn snapshot_with_escrow(c: &mut Curve, supply: u64, escrow: u64) {
    let organic = c.raised - escrow;
    c.refund_basis_total    = c.total_basis;                              // B
    c.refund_basis_pool     = c.raised.min(c.total_basis);                // P = min(raised, B)
    c.refund_leftover       = organic.saturating_sub(c.total_basis);      // zero-basis leftover
    c.refund_escrow_surplus = c.raised - c.refund_basis_pool - c.refund_leftover; // escrow left after covering basis
    c.refund_supply         = supply;
}

/// basis refund for burning tokens: used × P / B (never more than your basis)
fn basis_pay(c: &Curve, used: u64) -> u64 { md(used, c.refund_basis_pool, c.refund_basis_total) }

/// escrow surplus: pro rata to tokens burned (creator and linked wallets get none)
fn escrow_share(c: &Curve, tokens_in: u64) -> u64 { md(c.refund_escrow_surplus, tokens_in, c.refund_supply) }

/// insurance top-up budget: at most 10% of P, never more than this coin paid into insurance
fn topup_want(basis_pool: u64, contributed: u64) -> u64 { md(basis_pool, 1_000, 10_000).min(contributed) }

/// each refund's top-up is pro rata, so claim order doesn't change anyone's share
fn topup_share(c: &Curve, pay: u64) -> u64 {
    md(pay, c.topup_budget, c.refund_basis_pool).min(c.topup_budget.saturating_sub(c.topup_paid))
}
```

The escrow fills any basis shortfall first; only what's left over is shared per token. Every term is a fixed fraction
of the snapshot, so the vault can't be overdrawn and claiming early or late makes no difference.
