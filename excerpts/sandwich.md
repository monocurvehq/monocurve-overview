# Sandwich protection

```rust
const MAX_QUOTE_AGE_SLOTS: u64 = 150;        // a quote is valid for ~60 s at most
const ONCHAIN_MAX_SLIPPAGE_BPS: u64 = 1_000; // hard backstop (10%); the app defaults to 2%

fn check_deadline(slot: u64, deadline: u64) -> Result<()> {
    require!(slot <= deadline, ErrorCode::StaleQuote);
    require!(deadline <= slot.saturating_add(MAX_QUOTE_AGE_SLOTS), ErrorCode::DeadlineTooFar);
    Ok(())
}
```

- **Min-out is mandatory.** It must be above zero and within the 10% backstop of what the same trade returns at the
  price from the *start* of the slot, so a front-run inside the slot can't widen the victim's window.
- **Same-slot guard.** One wallet can't buy and sell the same coin in one slot (the classic buy → victim → sell).
  The program remembers the last 16 traders per coin per slot.
- TP/SL triggers need the price to hold past the level for 13 slots, and stop-losses also need the recent average price
  past the stop, so a keeper or trader can't move the price for a moment to trigger an order.
