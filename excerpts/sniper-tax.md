# Sniper tax

```rust
const SNIPER_TAX_MAX_BPS: u64 = 9_900;      // 99% in the launch slot
const SNIPER_TAX_HOLD_SLOTS: u64 = 150;     // first minute (~400 ms per slot)
const SNIPER_TAX_HOLD_END_BPS: u64 = 9_350; // still 93.5% after one minute
const SNIPER_TAX_SLOTS: u64 = 2_250;        // 0% from 15 minutes on

/// tax in basis points for a buy `d` slots after launch
fn tax_bps_at(d: u64) -> u64 {
    if d < SNIPER_TAX_HOLD_SLOTS {
        // first minute: 99% -> 93.5%, linear
        SNIPER_TAX_MAX_BPS - (SNIPER_TAX_MAX_BPS - SNIPER_TAX_HOLD_END_BPS) * d / SNIPER_TAX_HOLD_SLOTS
    } else if d < SNIPER_TAX_SLOTS {
        // to 15 minutes: 93.5% × ((2250 − d) / 2100)³, rounded up
        let r = (SNIPER_TAX_SLOTS - d) as u128;
        let t = (SNIPER_TAX_SLOTS - SNIPER_TAX_HOLD_SLOTS) as u128;
        (SNIPER_TAX_HOLD_END_BPS as u128 * r * r * r).div_ceil(t * t * t) as u64
    } else {
        0
    }
}
```

The tax is taken off the top of the buy (the 0.90% fee is charged on the rest), goes 100% to the coin's holder-rewards
pot, and isn't part of the buyer's refund basis. Sells never pay it.
