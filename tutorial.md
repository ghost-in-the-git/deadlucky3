# Dead Lucky — Tutorial Script

## Voice & Tone
Narrator is a quirky "professor" character. Warm, slightly dramatic, short sentences.
The player is addressed directly. No walls of text — one idea per beat.

---

## Pre-set Game State
Before the tutorial starts the engine must set up a controlled board so every
step is guaranteed to play out correctly.

| Item | Value | Reason |
|------|-------|--------|
| Tile 0 (top-left) | **3** | matches forced roll |
| Tiles 1–8 | random or fixed — TBD | need a straight & triple in later steps |
| First roll | forced **3** | guaranteed match on tile 0 |
| Die type | Gambler (default) | no special powers to confuse things |
| Hand | full 9 dice | normal start |

> **Open question:** steps later in the flow ask the player to trigger a straight
> and a triple. We need to decide whether to (a) pre-set specific tiles so the
> player can engineer them naturally, or (b) script a second forced placement
> sequence. Needs resolving before code.

---

## Step-by-Step Flow

### CARD — Welcome
> *"Welcome back, professor! Looks like you lost your memory in that last test —
> let's get you rolling again."*

Action: tap **Start** or **Skip Tutorial**

---

### ACTION — Select a die
> *"Step one: tap a die in your hand to select it."*

- Hand section glows, all other sections dimmed
- Input locked to hand only
- Advances automatically when a die is selected

---

### ACTION — Roll
> *"Good. Now tap it again to roll it."*

- Only the selected die slot is tappable
- Roll result is forced to **3**
- Advances automatically after roll animation settles

---

### ACTION — Place
> *"It's a 3. Put it on the board — let's go top left."*

- Board section visible, tile 0 (top-left) glows
- Only tile 0 is tappable
- Advances automatically after die is placed

---

### CARD — Points on the tile
> *"Boom — die placed. You can see it has some points sitting on it."*

Action: tap **Next**

---

### CARD — Scoring explained
> *"Points are a mix of the roll value, the board value, the combo multiplier,
> and each die's own powers. It's deep — but to get started you just need to
> roll and combo."*

Action: tap **Next**

---

### CARD — Dice don't pay out yet
> *"Dice don't give you their points until they come back to your hand."*

Action: tap **Next**

---

### CARD — How to get dice back
> *"So how do you get dice back? Glad you asked."*

Action: tap **Next**

---

### CARD — There are 4 combo types
> *"You have to combo them. There are 4 types of combos."*

Action: tap **Next**

---

### CARD — Combo 1: Match
> *"First: a Match. If the number you rolled matches the number on the tile,
> that die scores and returns. You can tell when it's matched — it flips to a 9."*

Action: tap **Next**

---

### CARD — Combo 2: Straight
> *"Second: a Straight. Place three dice in a row with consecutive numbers —
> like 1, 2, 3. All three return."*

Action: tap **Next**

---

### CARD — Combo 3: Triple
> *"Third: a Triple. Fill a row with three matching numbers — like 5, 5, 5.
> All three return."*

Action: tap **Next**

---

### CARD — Combo 4: [TBD]
> *"[Fourth combo type — needs copy once confirmed]"*

Action: tap **Next**

> **Open question:** what is the 4th combo type? Diagonal? Luck-based?
> Needs confirming from design before this card is written.

---

### ACTION — Place the last die (trigger returns)
> *"Go ahead — place one more die. That'll trigger the return."*

- Specific tile TBD (must be set up to guarantee a combo fires)
- Input unlocked for board only
- Advances after the return animation completes

---

### CARD — Points collected
> *"There — your score went up. That's your dice paying out as they came home."*

Action: tap **Next**

---

### CARD — The goal
> *"That's the game. Roll, place, combo, collect. Get as many points as you can
> across 100 returns."*

Action: tap **Next**

---

### CARD — Returns counter
> *"You can see your return count up here."*

- Optionally highlight the returns counter in the header
Action: tap **Next**

---

### CARD — Game over condition
> *"One warning: if you ever place a die and nothing returns to your hand —
> that's an instant game over. Don't let the board go cold."*

Action: tap **Next**

---

### CARD — Hand-off
> *"OK — I'll throw you into a real game now. Good luck, professor."*

Action: tap **Play** → tutorial ends, real game begins

---

## Open Questions Before Implementation

1. **4th combo type** — what is it?
2. **Pre-set tile layout** — which tile values guarantee the player can trigger a straight and a triple during the tutorial without it feeling forced?
3. **Second forced placement** — do we script the second action step (the "trigger a return" step) with a forced roll, or let it play free and wait for any return?
4. **Returns counter highlight** — do we add a pointer/arrow to the header, or just describe it in copy?
5. **Character name** — is "professor" the locked name, or placeholder?
