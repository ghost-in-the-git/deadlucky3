# Dead Lucky — Tutorial Script

## Voice & Tone
Narrator is a quirky "professor" character. Warm, slightly dramatic, short sentences.
The player ("professor") is addressed directly. One idea per beat, no walls of text.

---

## Pre-set Game State

Board is 7/9 filled before the tutorial starts. Player places exactly 2 dice.

### Board layout

```
[ ]  4   3        slot 0 = empty  →  player placement 1
 5   5   3        slot 3 = empty  →  player placement 2 (below slot 0, left column)
 1   2   3
```

| Slot | State | Die face | Tile value | Notes |
|------|-------|----------|------------|-------|
| 0 | **empty** | forced **3** | **3** | match guaranteed |
| 1 | pre-placed | 4 | 2 | filler, no combo |
| 2 | pre-placed | 3 | 6 | part of right-col triple |
| 3 | **empty** | forced **5** | **5** | match guaranteed |
| 4 | pre-placed | 5 | 1 | filler, no combo |
| 5 | pre-placed | 3 | 4 | part of right-col triple |
| 6 | pre-placed | 1 | 6 | part of bottom-row straight |
| 7 | pre-placed | 2 | 5 | part of bottom-row straight |
| 8 | pre-placed | 3 | 1 | part of right-col triple + bottom-row straight |

### Combos that fire at end of turn (after both placements)

| Combo | Tiles | How |
|-------|-------|-----|
| Match | 0 | face 3 = tile 3 |
| Match | 3 | face 5 = tile 5 |
| Triple | 2, 5, 8 | faces 3, 3, 3 |
| Straight | 6, 7, 8 | faces 1, 2, 3 |

**Total returning: 7 dice** (slots 0, 2, 3, 5, 6, 7, 8)

Verified — no accidental combos from the forced placements:
- Row 1 post-placement: 5, 5, 3 — not a triple, not a straight ✓
- Col 0 post-placement: 3, 5, 1 — not a straight ✓
- No cross fires ✓

### Hand
- 2 Gambler dice (default die type, no special powers)
- No other dice in hand

---

## UI Pointer
A small white dot (pulsing) appears near UI elements when they are referenced
in copy. No arrow — just a dot close to the element. Fades out on the next beat.
Appears near: score strip (on "points on tile" beat), returns counter (on "count
up here" beat).

---

## Step-by-Step Flow

### CARD — Welcome
> *"Welcome back, professor! Looks like you lost your memory in that last test —
> let's get you rolling again."*

- Board is visible behind the card with 7 dice already placed
- Buttons: **Let's go** / **Skip tutorial**

---

### ACTION — Select a die
> *"Step one — tap a die in your hand to select it."*

- Hand section glows, everything else dimmed
- Input locked to hand only
- Auto-advances when any die is selected

---

### ACTION — Roll
> *"Good. Now tap it again to roll it."*

- Only the selected die slot is tappable
- Roll is forced to **3**
- Auto-advances after roll animation settles

---

### ACTION — Place (slot 0)
> *"It's a 3. Top left — go on."*

- Board highlighted, slot 0 (top-left) pulses
- Only slot 0 is tappable
- Auto-advances after die is placed and match state is shown (die flips to 9)

---

### CARD — Match explained
> *"See how it turned into a 9? That means it matched the tile — that's a Match combo.
> Matched dice always come back."*

- White dot pulses near the 9 on slot 0
- Button: **Next**

---

### CARD — Points sitting on tiles
> *"But it hasn't scored yet. Dice hold their points on the board until they
> return to your hand."*

- White dot pulses near the score strip
- Button: **Next**

---

### ACTION — Place (slot 3)
> *"One more. There's only one spot left — go ahead."*

- Board highlighted, slot 3 (middle-left) pulses
- Only slot 3 is tappable
- Roll is forced to **5** (match with tile 3 = 5)
- Auto-advances after placement — hand is now empty, return sweep begins

---

### CARD — (shown after return animation completes)
> *"There we go. Seven dice came home — and your score jumped."*

- White dot pulses near the score total in header
- Button: **Next**

---

### CARD — How returns work
> *"Dice come back when they combo. There are four types."*

- Button: **Next**

---

### CARD — Combo 1: Match
> *"A Match — your roll equals the tile number. The die turns 9 and comes home."*

- Button: **Next**

---

### CARD — Combo 2: Triple
> *"A Triple — three dice in a line with the same number, like those 3s on the
> right. All three return."*

- Right column (slots 2, 5, 8) briefly re-highlights with combo glow
- Button: **Next**

---

### CARD — Combo 3: Straight
> *"A Straight — three dice in a line with consecutive numbers. 1, 2, 3 along
> the bottom — done."*

- Bottom row (slots 6, 7, 8) briefly re-highlights with combo glow
- Button: **Next**

---

### CARD — Combo 4: Cross
> *"A Cross — two lines through the middle tile, both forming a combo at the
> same time. Rare. Pays well."*

- Button: **Next**

---

### CARD — The goal
> *"That's the game. Roll, place, combo, collect. You get 100 returns total —
> make every one count."*

- White dot pulses near the returns counter in the header
- Button: **Next**

---

### CARD — Game over condition
> *"One thing — if you ever place a die and nothing comes back, it's over.
> Don't let the board go cold."*

- Button: **Next**

---

### CARD — Hand-off
> *"OK, professor — real game now. Good luck."*

- Button: **Play** → tutorial UI clears, player continues from the live tutorial
  state (2 dice still on board at slots 1 and 4, 7 dice back in hand)

---

## Resolved Questions

| # | Question | Answer |
|---|----------|--------|
| 1 | 4th combo type | **Cross** — two qualifying lines through centre simultaneously |
| 2 | Pre-set tile layout | Confirmed above — 7 pre-placed, 2 forced placements |
| 3 | Second forced placement | **Yes** — forced roll 5, tile 5 = 5, guaranteed match |
| 4 | Returns counter highlight | White pulsing dot near the element, no arrow |
| 5 | "Professor" | NPC/narrator voice — how the character addresses the player |

## Remaining Before Implementation

Nothing — all decisions confirmed. Ready to build.
