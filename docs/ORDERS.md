# Take-profit & stop-loss

Take-profit, stop-loss and trailing stops run **on the bonding curve**, enforced by the program.

## How it works

- **Your tokens stay in your wallet.** Placing an order only gives the order a spending approval for the amount you
  set. Cancel any time; unused keeper tips come back.
- **Anyone can fire it.** Orders are executed by a permissionless keeper: any keeper can trigger an order once its
  condition is met, for the small tip you pre-paid (at most 0.01 SOL per fill). Monocurve runs one, but doesn't have to
  be the one that fires it.
- One order per wallet per coin, with an optional take-profit, stop-loss or trailing stop.
- On graduation or refund mode, open orders are cancelled and tips refunded. Orders don't move to the DEX pool.

## When an order fires

- **A short spike or dip can't trigger anything.** The price has to stay past your level for about 5 seconds
  (13 slots), through every trade in that time.
- **Stop-losses have one more check:** the coin's recent average price, over roughly the last 30 slots (about
  12 seconds), must also be at or below your stop. So a stop fires only when the drop has actually held, not when one
  large sell briefly pushes the price through it. On a sharp, lasting drop that means it fires a few seconds after the
  price crosses your stop; on a slower slide it fires as soon as the average catches up.
- Take-profits only need the 5-second hold.

## Fill price

- Once triggered, the order sells at the curve's price at that moment.
- A take-profit accepts at most your order slippage (0.1% to 10%).
- A stop-loss allows at least 7% (or your slippage, if higher) so it still fills in a fast drop. It can fill below
  your stop.

## Minimum stop distance

- In the app, a stop-loss must be **at least 3% below the current price**, and a trailing stop needs a trail of
  **at least 3%**. Closer stops are refused because normal price noise would set them off.
- Stops between 3% and 8% below the price are allowed, with a warning that they may fire on normal swings,
  especially on quiet coins.
- The app re-checks the 3% rule against a fresh price just before you sign.
- This minimum is an app rule. The program itself accepts trailing stops down to 1%, so an order built by hand
  outside the app can be tighter than 3%; it still has to pass the 5-second hold and the average-price check above.

## Limits

- Orders need a keeper to fire them. If no keeper is running, an order waits.
- The fill price isn't guaranteed beyond your slippage. In a crash, a stop-loss can sell well below your stop.
- Orders work on the bonding curve only, not on the DEX pool after graduation.
