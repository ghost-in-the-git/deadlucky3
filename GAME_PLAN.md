# Dead Lucky — Game Design Plan

## Overview

Dead Lucky is a single-player dice placement game. The player rolls dice, places them on a board, and earns points. Dice that form matching patterns on the board return to the player's hand, keeping the game alive. The goal is to score as high as possible before the session ends.

---

## Screen Layout

```
┌─────────────────────────┐
│   BOARD  (3×3 grid)     │  ← placed dice live here, retain value until returned
├─────────────────────────┤
│   SCORE INFO STRIP      │  ← FACE / TILE / POWER / COMBO / LUCK breakdown
├─────────────────────────┤
│   HAND   (3×3 grid)     │  ← player's available dice to select and roll
└─────────────────────────┘
```

---

## Core Loop

Each turn the player works through their full hand:

1. **Select** — tap a die in the hand grid to select it
2. **Roll** — tap the selected die again to roll it (d6)
3. **Place** — choose an empty board tile to place the rolled die on
4. **Score** — points are calculated immediately on placement
5. **Repeat** — continue until all 9 hand slots have been played
6. **Evaluate** — board is checked for return conditions
7. **Return** — qualifying dice come back to the player's hand
8. **Next turn** — player plays again with whatever dice returned

---

## Placement Scoring

When a die is placed on a board tile, four values combine to produce the placement score:

| Component | Source | Notes |
|-----------|--------|-------|
| **Face**  | Number rolled on the die | 1–6 |
| **Tile**  | Fixed value of the board position | Set per tile |
| **Power** | Unique ability of the die type | Adds, multiplies, or modifies score |
| **Luck**  | Combo streak multiplier | Increases with consecutive successful placements |

> Example from mockup: Face 9 + Tile 1 + Plain +1 = 11 points, Combo ×1, Luck 1

---

## Board Persistence

- Dice placed on the board **stay there** between turns
- They hold their accumulated points until a return condition is triggered
- A tile occupied by a die cannot be used again until that die returns
- This means the board gradually fills — the player must engineer returns to free space and reclaim dice

---

## Return Conditions

At the end of each turn the board is evaluated. A die returns to the hand if it satisfies any of the following:

### 1. Match
The rolled face value equals the tile value it was placed on.

### 2. Row Triple
Three dice in the same row share the same face value.
- e.g. `5 · 5 · 5` or `3 · 3 · 3`

### 3. Row Straight
Three dice in the same row form a sequential run.
- e.g. `1 · 2 · 3` or `4 · 5 · 6`

> Rows can be horizontal, vertical, or diagonal — **TBD: confirm which axes count**

---

## Dice Types

Each die has a unique **Power** that modifies how it scores or behaves. The Plain die is the baseline:

| Die | Level | Power |
|-----|-------|-------|
| Plain | Lv.1 | Just a normal die — no modifier |
| *(more TBD)* | | |

Powers can include flat bonuses, multipliers, conditional effects, or return condition modifiers.

---

## End Conditions

The session ends when either of the following is true:

| Condition | Description |
|-----------|-------------|
| **Empty hand** | No dice returned at the end of a turn — the player has nothing left to play |
| **100 returns** | The cumulative total of dice returned across all turns reaches 100 |

The 100-return cap rewards skilled, efficient play over grinding. A player who engineers frequent returns will hit the cap with a high score; a player who places poorly will run out of dice first.

---

## Scoring Summary

- Points accumulate per placement throughout the session
- Dice that stay on the board hold their points in reserve
- When a die returns, its held points are added to the total score
- Final score is the sum of all points at session end

> **TBD:** Confirm whether points score on placement, on return, or both

---

## Open Questions

- [ ] Do rows count in all directions (horizontal, vertical, diagonal)?
- [ ] Can a die satisfy multiple return conditions at once — does that multiply the return value?
- [ ] What happens to points held by dice still on the board when the session ends — lost or counted?
- [ ] Is there a visual indicator showing which dice on the board currently qualify for return?
- [ ] How is the Luck/combo streak defined — consecutive returns, consecutive placements, or something else?
- [ ] How many dice types are planned for launch?
