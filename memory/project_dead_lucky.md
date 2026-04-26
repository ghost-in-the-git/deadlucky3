---
name: Dead Lucky game concept
description: Core mechanics and structure of the Dead Lucky dice placement game
type: project
---

Single-player score-chasing dice placement game. Session ends at 100 total returns or empty hand.

**Layout:** 3×3 board (top) / info strip (middle) / 3×3 hand (bottom)

**Turn loop:** select die → roll → place on board tile → score on placement → end of turn evaluate board for returns

**Placement score = Face + Tile + Dice Power + Luck (combo)**

**Return conditions:** match (face = tile value), row triple (3 same values in a row), row straight (3 sequential in a row)

**Board persistence:** unreturn dice stay on board between turns, holding their points, blocking their tile

**End:** no dice return (hand empty) OR 100 cumulative returns hit

**Why:** 100-return cap rewards skill over marathon play — efficient players hit cap with high score, poor players run out of dice

**How to apply:** features should reinforce the tension between placing dice for score vs engineering returns to stay alive
