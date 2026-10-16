# Fee split

```rust
// 0.90% of every trade, in basis points of the trade
const FEE_BPS: u64 = 90;
const PLATFORM_FEE_BPS: u64 = 25;  // 0.25%
const CREATOR_FEE_BPS: u64 = 20;   // 0.20%
const BUYBACK_FEE_BPS: u64 = 30;   // 0.30%  buyback & burn
const INSURANCE_FEE_BPS: u64 = 15; // 0.15%
const _SUM_OK: () = assert!(PLATFORM_FEE_BPS + CREATOR_FEE_BPS + BUYBACK_FEE_BPS + INSURANCE_FEE_BPS == FEE_BPS);

// community boost: extra creator bps per tier, carved out of buyback (0.20% / 0.23% / 0.26% / 0.30%)
const BOOST_BPS: [u64; 4] = [0, 3, 6, 10];

/// (platform, creator, buyback, insurance) of `fee`. Buyback keeps the rounding dust,
/// so the four always sum to exactly `fee`.
fn split_fee_boost(fee: u64, extra_bps: u64) -> (u64, u64, u64, u64) {
    let extra = extra_bps.min(BUYBACK_FEE_BPS);
    let platform  = (fee as u128 * PLATFORM_FEE_BPS as u128 / FEE_BPS as u128) as u64;
    let creator   = (fee as u128 * (CREATOR_FEE_BPS + extra) as u128 / FEE_BPS as u128) as u64;
    let insurance = (fee as u128 * INSURANCE_FEE_BPS as u128 / FEE_BPS as u128) as u64;
    (platform, creator, fee - platform - creator - insurance, insurance)
}
```

On a buy, the fee is taken from the SOL in; on a sell, from the SOL out. The buyback slice is immediately spent on
the curve and the tokens it buys are burned.
