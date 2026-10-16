# Insurance pool

```rust
const DEFAULT_POOL_SHARE_BPS: u16 = 3_000;          // 30% of the platform remainder
const DEFAULT_POOL_CAP: u64 = 500_000_000_000;      // 500 SOL
const BPS: u64 = 10_000;

/// (to_pool, to_revenue) for the platform fee left after referrals.
/// The pool never receives more than the room left under its cap.
fn split_platform(remainder: u64, pool_share_bps: u16, pool_balance: u64, pool_cap: u64) -> (u64, u64) {
    let want = (remainder as u128 * pool_share_bps.min(BPS as u16) as u128 / BPS as u128) as u64;
    let to_pool = want.min(pool_cap.saturating_sub(pool_balance));
    (to_pool, remainder - to_pool)
}
```

The dedicated 0.15% insurance slice goes through the same function with a 100% share, so it also fills the pool only up
to its cap; anything above the cap goes to platform revenue. The pool has **no admin withdrawal**: only refund
top-ups, rug refunds and graduation bonuses pay out, and together they take at most 10% of the pool per 24 hours.
