# Dead Lucky — Summon & Sacrifice

## Overview

The collection-growth loop. Players spend **chips** at the Summon Shrine to roll random dice from the full 17-type pool. Unwanted duplicates are **sacrificed** for chips and **shards** — a universal crafting currency spent at the **Forge** to hand-pick dice from a daily rotating roster.

There are no rarity tiers, no pity, and no randomness at the Forge. The gacha side is pure RNG; the sacrifice/forge side is pure agency. The two halves balance each other.

---

## Currencies

| Currency | Earned From | Spent On |
|----------|-------------|----------|
| **Chips** | Daily leaderboard payout (end-of-day rank-based) · Daily missions *(spec TBD)* · Sacrifice refunds | Summons |
| **Shards** | Sacrifice only | Forge |

Chips are **not earned in-session**. Playing the game raises your leaderboard position; the leaderboard pays out chips at the daily reset. This keeps the in-session score pure (not a currency grind) and ties economy pacing to the once-per-day rhythm.

---

## Summon Shrine

### Pool
All **17** dice types are eligible on every pull, at **equal flat odds** (~5.88% each). Starters are included — skins and power levels (both TBD future systems) will make starter pulls meaningful beyond pure dupes.

### Pulls
| Pull | Cost | Yield |
|------|------|-------|
| **Single summon** | 200 chips | 1 die |
| **10-pull** | 2000 chips | 10 dice |

> **TBD:** whether the 10-pull gets a discount or a guaranteed sweetener (currently flat price).

### Outcome
Every summoned die is added to the player's **Inventory** (see below). There is no "new vs dupe" distinction at the shrine — both go to the same pile. The player sees what they rolled and can sacrifice dupes immediately from a post-pull screen.

---

## Dice Inventory

The player's collection is a **count per die type**, not a boolean owned/locked state.

- A die type is **unlocked** the moment inventory count ≥ 1.
- Inventory is **uncapped** — keep as many dupes as you like.
- Owning ≥ 1 of a die type lets it be equipped in **any or all** of the 3 decks. Equipping does **not** consume a copy; a deck slot is a reference, not a hold.

### Sacrifice protection
You cannot sacrifice your **last** copy of a die type. The system enforces `count ≥ 2` before allowing sacrifice. This guarantees a die, once unlocked, stays unlockable across all decks forever.

---

## Sacrifice

Burn a die from inventory to recoup materials.

| Yield per die sacrificed | Amount |
|--------------------------|--------|
| **Shards** | 6 |
| **Chips** | *TBD — propose 50* |

Flat yield across all 17 types. Rarity doesn't exist, so neither does a sacrifice curve.

### Where sacrifice happens
- **Post-pull screen** after a summon — quick-sacrifice any duplicates rolled
- **Inventory browser** — bulk sacrifice from stored dupes at any time

---

## Forge

The deterministic counterpart to the shrine. The Forge exchanges shards for a specific die, hand-picked by the player.

### Rotating roster
Every **24 hours (UTC 00:00 reset)** the Forge offers a rotating selection of dice available to craft. The player arrives, sees what's on offer, and chooses.

- Roster size: **TBD — propose 3 dice per day**
- Selection: **TBD** — pure random from the 17-pool, or weighted to guarantee variety across a week?

### Cost
- **TBD — propose 60 shards per forge** (= 10 sacrifices worth)

Flat across all dice. No forge discount for dice already owned; a forged die simply adds +1 to that type's inventory count (which feeds right back into the sacrifice loop).

### Gating
Forge works on **any** die in the daily roster regardless of whether the player has ever seen it. The Forge is the deterministic path to unlock dice the RNG refuses to give you.

---

## Economy Sources

| Source | Payout | Cadence |
|--------|--------|---------|
| **Leaderboard rank payout** | Chips scaled by daily rank | End of UTC day, resets leaderboard |
| **Daily missions** | Chips *(structure TBD — paired with Player Profile system)* | UTC day |
| **Sacrifice refund** | 6 shards + ~50 chips per die | On demand |

Chips flow in once per day from rank + missions; shards flow in whenever the player sacrifices. The two currencies feed two different decision styles (RNG pull vs. deterministic craft).

---

## UI Plan

The three systems share a single entry point from Home: a **Shrine** tile that opens a hub. The hub routes to Summon, Forge, and Inventory. A currency strip showing **Chips · Shards** sits at the top of every screen in this flow so balances are always visible.

### Shrine hub

```
┌─────────────────────────┐
│ [←]  SHRINE   Chips·Shards│
├─────────────────────────┤
│  ┌────────┐ ┌────────┐  │
│  │ SUMMON │ │ FORGE  │  │
│  └────────┘ └────────┘  │
│  ┌──────────────────┐   │
│  │   INVENTORY       │  │
│  └──────────────────┘   │
└─────────────────────────┘
```

Two square cards side-by-side for the active systems, one full-width card below for Inventory browsing. The Forge card shows its rotation countdown as a subtitle so the player knows when to come back.

### Summon screen

- Large chip balance up top
- Two buttons: **Summon ×1** (200 chips) and **Summon ×10** (2000 chips)
- Disabled states when balance is insufficient
- Tap → reveal transition (fade-in grid for MVP; sparkle polish later)

### Post-pull reveal

- 1- or 10-die grid, each entry shows the die face + type name + "NEW" flag if this pull crossed `count: 0→1`
- Per-entry **Sacrifice** button (disabled if sacrificing would drop count below 1)
- Shortcut button: **Sacrifice all duplicates** — burns every entry whose resulting count would still be ≥ 1
- **Continue** returns to Shrine hub

### Inventory browser

Reuse the existing 17-type collection grid layout. Each entry gains:

- **Count badge** in the corner (e.g. `×4`)
- **Sacrifice** control, only enabled when count ≥ 2
- Locked dice (count 0) stay as `???` silhouettes

Tapping an entry opens a small sacrifice sheet with a quantity stepper (1 → count−1) and a preview of total chip + shard yield. Confirm to execute.

### Forge screen

- Countdown to next roster rotation at top (UTC 00:00)
- Shard balance
- 3 die cards (today's roster), each showing: die face, type name, shard cost, **Forge** button
- Forge button disabled when shards < cost
- Confirm sheet on tap → die added to inventory, shard balance decrements, button becomes "Forge again" (no daily cap per die — as long as shards hold out)

> **TBD:** whether the same die can be forged multiple times in one day, or if each roster slot is one-shot per 24h.

### Layout tradeoffs (locked)

- **Hub over flat tiles** — Home stays clean; the shared currency strip lives inside the hub rather than cluttering the in-game header.
- **Inventory + sacrifice co-located** — one screen instead of splitting a "Sacrifice" flow. Denser but fewer taps, and matches how players actually think ("I want to clean up my dupes" is one intent, not two).
- **Reveal is minimum-viable** — fade-in grid only. Animation polish (roll-and-reveal, dupe shatter) is a post-wireframe pass.

---

## Open Questions / TBD

- [ ] 10-pull pricing — flat 2000 or discounted?
- [ ] Chip yield per sacrifice (proposed 50)
- [ ] Forge cost in shards (proposed 60)
- [ ] Daily forge roster size (proposed 3)
- [ ] Forge roster selection rule — pure random or variety-weighted?
- [ ] Leaderboard payout curve — flat by rank tier, exponential, top-N only?
- [ ] Daily missions structure (deferred — lives with Player Profile design)
- [ ] Skins + power levels — how do starter dupes become valuable once these land?
- [ ] Post-pull flow — auto-show sacrifice prompt, or route to a results screen first?
