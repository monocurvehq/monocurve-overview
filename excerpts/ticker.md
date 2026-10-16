# One coin per ticker

Before a ticker is registered it is normalised. If the normalised form is already taken by a live or graduated
coin, the launch fails.

1. **Fold characters.** Upper-case; full-width letters and digits to ASCII; Cyrillic and Greek look-alikes to their
   Latin letter. Everything else (spaces, `$`, punctuation, emoji) is dropped.
2. **Strip version suffixes.** `PEPE2`, `PEPE69`, `PEPEV2` → `PEPE`.
3. **Strip derivative affixes.** One prefix (`BABY`, `MINI`) and one suffix (`CLASSIC`, `TOKEN`, `COIN`, `INU`, `AI`),
   only if 3+ characters remain. `BABYDOGE`, `DOGEINU`, `DOGEAI` → `DOGE`. Disguised affixes count too
   (`B4BYFROG`, `FR0G1NU` → `FROG`).
4. **Repeat step 2.** `BABYPEPE2` → `PEPE`.
5. **Leetspeak digits.** `0→O 1→I 3→E 4→A 5→S 7→T 8→B`. `P3PE` → `PEPE`, `B0NK` → `BONK`.

A ticker is released only when its coin fails without graduating (refund mode). Graduated coins keep their ticker
forever. After a release, the wallet that launched the failed coin, and wallets linked to it by funding, can't launch
that ticker or a look-alike for **30 days**. Other wallets can.
