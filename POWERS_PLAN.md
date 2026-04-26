# Dead Lucky — Dice Powers & Deck Builder Plan

## Overview

Every die type has a unique **Power** that modifies how it rolls, places, or scores. Before a session the player assembles a **deck** — 9 dice picked from their **collection** and pinned to specific hand slots. A session is played with exactly that deck.

This doc covers three interlocking pieces:

1. **Power system** — the runtime that lets any die hook into roll / place / score / return events.
2. **Deck builder** — the pre-session UI for composing a 9-die deck from the collection.
3. **Chips** — a currency stub. Awarded by certain powers, unused for now.

---

## Deck

### Rules
- A deck is exactly **9 dice**, one per hand slot (slot 0 … slot 8).
- Each die type may appear **at most once** per deck.
- The deck is **position-aware**: slot 3 in the deck becomes the die at hand slot 3 on session start. This lets the player design placement patterns around where each die will be.
- A deck is **inaccessible** once a session starts (not "locked" — just not reachable from the in-game menu). Editing happens only from the home screen.

### Starter collection
The player begins with the **first 9 die types** from the launch list (`Plain, Lucky, Moth, Flight, Sun, Moon, Angel, Demon, Reaper`). The remaining 8 types (`Copycat, Dagger, Sniper, Gambler, Vampire, Lost Love, Snake, Trinity`) show as **locked** (`???`) entries in the collection browser. The default deck fills all 9 slots with the 9 owned dice in list order; the player can shuffle them around between slots. Acquisition/unlocks are deferred.

### Persistence (localStorage)
```
dl_username     : string
dl_collection   : string[]                       // array of die type IDs owned
dl_decks        : (string|null)[3][9]            // three decks, each slot → type ID
dl_active_deck  : 0 | 1 | 2                      // which deck launches on Play
dl_chips        : number
```
Deck saves live any time a slot changes. No "save" button.

---

## Deck Builder UI

### Entry
**Deck Builder** button on the home screen, below **Play**. The active deck is what launches when the player hits Play.

### Deck Builder screen
Mirrors the existing reference build:
```
┌──────────────────────────────────────────┐
│              DECK BUILDER                │  ← title, green
│                                          │
│   ┌───┐ ┌───┐ ┌───┐                      │
│   │ 0 │ │ 1 │ │ 2 │                      │  ← 3×3 deck slots, each
│   └───┘ └───┘ └───┘                      │     showing the assigned
│   ┌───┐ ┌───┐ ┌───┐                      │     die's visual preview
│   │ 3 │ │ 4 │ │ 5 │                      │
│   └───┘ └───┘ └───┘                      │
│   ┌───┐ ┌───┐ ┌───┐                      │
│   │ 6 │ │ 7 │ │ 8 │                      │
│   └───┘ └───┘ └───┘                      │
│                                          │
│       SKIN       DICE       AURA         │  ← three editable "layers"
│    ┌───────┐ ┌───────┐ ┌───────┐         │     for the selected slot
│    │DEFAULT│ │ PLAIN │ │ NONE  │         │
│    └───────┘ └───────┘ └───────┘         │
│                                          │
│                PLAIN                     │  ← selected die name
│         JUST A NORMAL DIE.               │  ← flavor / description
│                                          │
│       [ X ]  [ DECK 1 ]  [ ✓ ]           │  ← cancel / deck picker / confirm
└──────────────────────────────────────────┘
```

Key layers per slot:
- **SKIN**   — cosmetic skin applied to the die. Starts at `DEFAULT`.
- **DICE**   — the die type (the power). This is what the plan above is about.
- **AURA**   — ambient effect attached to the die. Starts at `NONE`.

The three layer tiles below the deck act as shortcuts: tapping one opens the corresponding collection browser for that layer, scoped to what the player owns.

### Interaction
- Tap a **deck slot** to select it — the three layer tiles and the name/flavor text update to reflect that slot's current assignment.
- Tap **SKIN / DICE / AURA** to open the collection browser for that layer.
- Bottom bar:
  - **X**        — cancel edits since entering the builder, return to home.
  - **DECK 1**   — deck selector. Tapping cycles between saved decks (DECK 1 / 2 / 3…). Long-press (or a secondary icon) lets the player rename.
  - **✓**        — confirm / save and return to home.

### Collection browser
Same visual language, opened from any of the layer tiles:
```
┌──────────────────────────────────────────┐
│   [ ← ]              1 / 21              │  ← back + position counter
│                                          │
│   ┌────┐ ┌────┐ ┌────┐                   │
│   │PLAIN│ │ ??? │ │ ??? │                 │  ← owned = named + preview
│   └────┘ └────┘ └────┘                   │     unowned = "???" + silhouette
│   ┌────┐ ┌────┐ ┌────┐                   │
│   │ ??? │ │ ??? │ │ ??? │                │
│   └────┘ └────┘ └────┘                   │
│   … scrolls vertically …                 │  ← green scrollbar on right
└──────────────────────────────────────────┘
```

- Counter reads `selected / total`, where `total` is the full catalog size for that layer (not just owned count). For DICE at launch that's `/ 17`.
- **Owned** dice render with their name, glyph/pip preview, and a small colour tag under them.
- **Unowned** dice render as `???` over a silhouette, non-interactive.
- Tap an owned entry → assigns it to the slot the builder came from, returns to the builder screen.
- The entry that corresponds to the slot's *current* assignment is highlighted (green outline in the reference).
- Scrolling is vertical; the current page is indicated by the tall bar on the right.

### Multiple decks
Three functional deck slots at launch: **DECK 1 / DECK 2 / DECK 3**. The bottom-bar deck picker cycles between them; each deck saves independently to localStorage. The active deck (the one currently shown in the builder) is the one that launches when the player hits Play from home. This lets us design the deck-selection and navigation flow against a real multi-deck state from day one.

### Future hooks (not MVP)
- Deck names (player-editable, replacing the `DECK 1` / `DECK 2` defaults)
- Share codes / seed-linked leaderboards
- Filters (by category, rarity, owned/locked)
- Stats per deck ("this deck averaged X score last 10 runs")
- Skins and Auras as real mechanics (see Scope note below).

### Scope note — Skins and Auras
The reference UI reserves three editable layers (SKIN / DICE / AURA) per slot. For the first power-system implementation we only need the DICE layer to be functional; SKIN and AURA slots exist in the UI but are fixed at `DEFAULT` / `NONE` and the corresponding collection browsers are empty (or show only the default entry). The visual scaffolding is in place so adding those systems later is a drop-in.

---

## Chips

Flat integer currency, persisted in `dl_chips`. Earned by specific powers (e.g. Rich) and awarded on run summary. Not spendable yet — reserved for the future shop/unlock loop. Shown on home screen and summary.

---

## Power System

### Lifecycle hooks
Each die type optionally implements any of:

| Hook               | Fires when                                       | Mutable context                         |
|--------------------|--------------------------------------------------|------------------------------------------|
| `onSessionStart`   | session begins, before first roll                | session-wide state (luck, chips, etc.)  |
| `onRoll`           | right after a new face is rolled                 | rolled face, hand snapshot               |
| `onPlace`          | die is placed on a tile, before match check      | face, tile, game state                   |
| `onMatch`          | placement matched the tile                       | game state                               |
| `onComboForm`      | a line through this die just completed           | line kind (triple/straight)              |
| `onScore`          | score is being computed for this die at return   | face, tile, power, combo, luck, bonuses |
| `onReturn`         | die is returning to hand, after scoring          | target slot, return face                 |
| `onEndOfTurn`      | end-of-turn sweep, once per turn                 | board state                              |

Launch powers only use `onRoll`, `onPlace`, `onScore`. `onMatch`, `onComboForm`, `onSessionStart`, `onEndOfTurn`, `onReturn` are scaffolded but unused until later additions.

### Scoring pipeline
Current formula lives inline as `((face + tile + power) × combo) + luck`. It becomes a pipeline of context mutations:

```js
ctx = {
  die, tileIdx,
  face, tile, power, combo, luck,
  flatBonus:  0,
  isMatch, inTriple, inStraight,
  board, session,
}
```

1. Seed `ctx` from the return snapshot (`ctx.power = die.state.power` so accumulator powers like Moth / Sun / Moon / Angel / Demon automatically roll in).
2. Call `die.onScore(ctx)` (if defined) — power can add to any term or bonus.
3. `score = (ctx.face + ctx.tile + ctx.power) * ctx.combo + ctx.luck + ctx.flatBonus`.
4. Add `score` to `game.totalScore`. Bump `game.triggers++` once per die whose power actually fired this return.

The same pipeline also backs `previewScore` on placed dice and the middle-strip inspection, so displayed numbers always match what the die will score on return. This keeps the formula readable and makes every power implementable as a single pure function.

### Rule-bending powers
Some powers change game rules, not just scoring. These go outside the `onScore` pipeline and plug into well-defined extension points so new rule-benders can be added without rewriting placement/return logic.

- **Return override** (Flight): the end-of-turn return sweep first evaluates natural returners (match / triple / straight). After that, Flight dice are added to the return set iff the natural set is non-empty. If the natural set is empty, Flights stay put and the turn triggers the normal bust check.
- **Match suppression** (Lost Love): `placeDie` currently flips `face → 9` and sets `isMatch` when `rolledFace === tile`. Lost Love short-circuits that branch, so nothing about the match side-effects (luck bump, triggers, tile class) fires even though the face value technically matched.
- **Face-at-placement override** (Vampire): handled in `onPlace` by rewriting `die.rolledFace` before the match check. The rest of the placement flow reads the new value unchanged.
- **Roll-face override** (Copycat / Dagger / Gambler): handled by the shared `onRoll` hook, which runs after the random face is picked but before the rotation tween. Last write wins, in `DIE_TYPES` declaration order.

### Session state
A small dictionary lives on `game.session` for cross-die effects and cross-turn memory:

```js
game.session = {
  lastRolledFace:    null,   // last face rolled by any die this session (Copycat reads)
  reaperReturns:     0,      // count of off-sheet returns (diagnostics; not user-facing)
  perDie: {                  // keyed by die instance id
    [id]: { /* per-die flags: e.g. firedTriggersThisTurn, … */ }
  },
};
```

Only Copycat needs this at launch, but the structure is shared so future inter-die powers can write here without new plumbing.

### Data model
```js
const DIE_TYPES = {
  plain:  { id: 'plain',  name: 'Plain',  flavor: 'Just a normal die.',
            onRoll: null, onPlace: null, onScore: null, onReturn: null },
  sniper: { id: 'sniper', name: 'Sniper', flavor: '+50 points when matched.',
            onScore: ctx => { if (ctx.isMatch) ctx.flatBonus += 50; } },
  // … one entry per type
};
```

A die *instance* is `{ typeId, instanceId, state }` where `state` is a per-run dictionary (`state.power`, `state.hasFired`, etc.). State resets on session start.

---

## Launch Powers (17)

Each die carries its own `state.power` number. Powers that bank a score contribution write into `state.power`, which is the value the middle-strip Power UI shows and which feeds the scoring formula at return time.

| #  | Name      | Hook       | Effect                                                                                                     |
|---:|-----------|------------|------------------------------------------------------------------------------------------------------------|
| 1  | Plain     | —          | No power. Baseline die.                                                                                    |
| 2  | Lucky     | onRoll     | When Lucky itself is rolled, `game.luck += 1`.                                                             |
| 3  | Moth      | onRoll     | When **any** die is rolled (including Moth), `moth.state.power += 1`. Accumulates across the session.      |
| 4  | Flight    | rule-bend  | Returns at end-of-turn even without a match/triple/straight, **unless** no other die would return that turn (then Flight stays too). Normal combo returns are unaffected. |
| 5  | Sun       | onPlace    | `state.power += 5 * (filled neighbours)`. 8-direction neighbourhood, evaluated at placement.               |
| 6  | Moon      | onPlace    | `state.power += 5 * (empty neighbours)`. 8-direction, evaluated at placement.                              |
| 7  | Angel     | onRoll     | `state.power = (dice still in hand at roll time)`. Banked; applied when Angel eventually returns.          |
| 8  | Demon     | onRoll     | `state.power = (empty hand spots at roll time)`. Banked; applied when Demon eventually returns.            |
| 9  | Reaper    | onRoll     | On Reaper's roll, pick a uniformly random **placed** die on the board and return it immediately. The chosen die is **not scored** and does **not** count toward `game.totalReturns`. Reaper proceeds to placement as normal. No-op if the board is empty. |
| 10 | Copycat   | onRoll     | On Copycat's roll, set its face = the last face rolled by **any** die earlier in this session. If nothing has rolled yet, Copycat rolls normally (1–6). |
| 11 | Dagger    | onRoll     | Rolls 1 or 6, 50/50.                                                                                       |
| 12 | Sniper    | onScore    | If `isMatch`, `flatBonus += 50`.                                                                           |
| 13 | Gambler   | onRoll     | Rolls only even faces (2/4/6) when `game.luck` is even (0 counts as even), only odd faces (1/3/5) when odd. |
| 14 | Vampire   | onPlace    | `rolledFace = min(rolledFace + tile, 6)`. Boosted face is the die's real face — it drives match detection **and** line checks. |
| 15 | Lost Love | rule-bend  | When Lost Love is placed on a tile with matching face, **no match** triggers (no flip to 9, no match luck, no triggers). Can still return via triples/straights using its rolled face. |
| 16 | Snake     | onScore    | If Snake is in a straight, `combo += 1` for that line's scoring (straights for this die = +4 combo).       |
| 17 | Trinity   | onScore    | If Trinity is in a triple, `combo += 1` for that line's scoring (triples for this die = +4 combo).         |

### Coverage check
- **Baseline**: Plain
- **Luck / meta**: Lucky
- **Score accumulators (banked in `state.power`)**: Moth, Sun, Moon, Angel, Demon
- **Flat score bonuses on return**: Sniper
- **Combo bumps**: Snake, Trinity
- **Roll overrides**: Copycat, Dagger, Gambler
- **Face-at-placement override**: Vampire
- **Return rule-benders**: Flight, Lost Love
- **Inter-die / board effects**: Reaper

---

## UI touchpoints in-game

- **Hand slot**: small badge in the corner showing the die's type (2–3 letters or glyph). Plain = no badge.
- **Placed die (inspection)**: existing stat strip stays as-is, but the Power box now shows `die.state.power` (0 for Plain, whatever Moth / Sun / Moon / Angel / Demon have banked otherwise). Tapping the Power box (or a dedicated info button) reveals the power name/description — deferred.
- **Rolled die in hand**: the middle strip now reflects the die's in-hand stats too — e.g. Angel shows its banked Power as soon as it's rolled, before being placed. This replaces the current "all zeros for any hand die" behaviour.
- **Summary screen**: Triggers counter now tracks actual power activations. Chips earned shown alongside Score.

---

## Implementation order

1. **Data + persistence**: `DIE_TYPES` registry (17 entries), `collection` seeded with the first 9 types, `dl_decks` × 3 seeded with the 9 owned in list order, `dl_active_deck = 0`, `dl_chips = 0` in localStorage.
2. **Type-driven dice**: route dice creation through the die type (every die carries `typeId` and a mutable `state` object). Verify Plain behaves identically to today.
3. **Scoring pipeline**: refactor the return-sweep scoring into a `ctx` that runs through `die.onScore(ctx)`. Seed `ctx.power` from `die.state.power` so accumulator powers (Moth, Sun, Moon, Angel, Demon) "just work" once they can write to state.
4. **onRoll powers**: Lucky, Moth, Dagger, Copycat, Gambler, Angel, Demon. All replace or extend the `rollDie` flow; the shared hook runs after the random face is chosen but before the rotation tween.
5. **onPlace powers**: Sun, Moon, Vampire. Vampire mutates `rolledFace` before the match check so match detection reuses the boosted value unchanged.
6. **onScore powers**: Sniper, Snake, Trinity. These plug into the pipeline from step 3.
7. **Rule-benders**: Flight (return-sweep special case), Lost Love (suppress the face→9 path in `placeDie`).
8. **Reaper**: requires a one-off out-of-band return during `rollDie` — the returning die skips the score pipeline and does not bump `game.totalReturns`. Guard against the empty-board case.
9. **Deck builder UI**: slots grid, collection list, detail panel, save-on-change.
10. **Polish**: hand-slot power badges, summary Triggers count, power description tooltip, chip counter on home + summary.

---

## Long-term (out of scope, flagged for later)

- **Acquisition**: shop between runs, mid-run rewards, daily drops. Chips become the price tag.
- **Progression**: per-die levels (`Plain Lv.1` from GAME_PLAN.md). Levels boost magnitudes, not rewrites.
- **Rarity tiers**: common / rare / legendary, affects shop odds and visual treatment.
- **Multiple decks / loadouts**: name + save slots, quick-switch.
- **Synergy tags**: decks score bonus if they include e.g. 3+ "scoring" dice, 3+ "combo" dice, etc.
- **Leaderboards tied to deck**: daily board pattern + deck hash = deterministic comparable run.
- **Active powers**: player-triggered abilities (re-roll, swap). Will require a new UI affordance (long-press? dedicated button on selected die?).
- **Consumables**: single-use items that aren't dice (one-off re-roll, tile swap).

---

## Open questions to revisit

- **Order of onRoll effects** — Copycat / Dagger / Gambler all override the rolled face, while Vampire *modifies* a rolled face at placement. Only one roll-overrider should ever live on a single die, but we should still pick a deterministic resolution (last-write-wins, in declaration order).
- **Moth and Reaper interaction** — when Reaper rolls and triggers a board return, Moth counts Reaper's roll (+1). The triggered return itself is not a roll, so it does not bump Moth. Confirming this is the intent.
- **Angel / Demon timing** — power is sampled at roll time, so Angel's value is `(hand count excluding Angel, since Angel is mid-roll)` vs `(hand count including Angel)`. Proposing: **excluding Angel itself**, since Angel has logically left the hand to be rolled.
- **Gambler on a luck=0 first roll** — 0 is even, so Gambler opens with an even face. Works fine, just flagging.
- **Flight's "unless it's the only returner"** — evaluated against *natural* returners (match / triple / straight) only, not against other Flights. Otherwise two Flights would mutually enable each other. Proposing: Flight does not count as a natural returner.
- **Triggers counter** — bump once per power that fired per die per turn (a single die with multiple active powers still counts once each).
- **Summary screen skin showcase** — the 3×3 final board currently renders pip faces; once dice have skins, it shows each die's skin.
