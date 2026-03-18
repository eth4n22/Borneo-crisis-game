# Economy Balance Analysis: Borneo Crisis Card-Based System

## 1. Current Economy Overview

### Current Settings:
| Parameter | Value |
|------------|-------|
| Starting Budget | 15 |
| Passive Turn Growth | +5 flat |
| Bonus Funding Card | 1 in 19 (5.3% chance) |
| Bonus Funding Reward | +15 budget |
| WHO Funding Event | +25 budget (guaranteed once, rounds 7-9) |
| WHO Funding Chance | 30% chance for random event, plus guaranteed |

### Card Costs:
| Card | Cost | Frequency in Deck |
|------|------|------------------|
| Spray DDT | 12 | 3 in 19 |
| Cat Drop | 15 | 3 in 19 |
| Awareness Campaign | 10 | 3 in 19 |
| Repair Thatch | 8 | 3 in 19 |
| Protect Grain | 6 | 3 in 19 |
| Medical Aid | 5 | 3 in 19 |
| Bonus Funding | 0 | 1 in 19 |

### Turn Flow:
1. Start of turn: +5 budget
2. Choose ONE: Draw Card (adds random card to hand) OR +10 immediate budget
3. Play selected cards (pay costs)
4. End turn → next player

---

## 2. Diagnosis: Why the Economy Feels Too Generous

### Root Cause Analysis:

**The core problem is simple math:**

- **Typical turn income**: +5 passive + (50% chance of choosing +10 = effective +5 from Draw/Gain choice)
- **Total effective income**: ~10 budget per turn

**But card costs are relatively low:**
- Average card cost: ~9.3 budget
- With 15 starting budget and ~10 per turn, a player can afford most cards by turn 2-3

**Key imbalance factors:**

1. **Passive income is too high relative to card costs**
   - +5 passive means players always have 5+ to spend
   - Combined with +10 option, players rarely feel "broke"

2. **The Draw Card vs +10 Budget choice is not meaningful**
   - Drawing a card is usually strictly better (adds strategic options)
   - Players only choose +10 when their hand is full (6 cards)
   - This is a **failsafe**, not a real choice

3. **Bonus Funding card is too common**
   - 5.3% per draw is roughly 1 in 19
   - Over 12 turns with ~3 draws per player, players see 2-4 Bonus Funding cards per game
   - Each gives +15, which is huge

4. **WHO Funding event is too generous**
   - +25 budget to ALL players
   - Guaranteed once, plus 30% chance of random events
   - This is an emergency injection that rarely feels "emergency"

5. **No meaningful economy pressure**
   - Players can usually play their best 2-3 cards per turn
   - Budget rarely constrains decisions
   - The interesting choice "should I play this card or save?" never happens

---

## 3. Evaluation of Interest Mechanics

### Option A: No Interest at All
**Pros:**
- Simple, predictable economy
- Easy for players to calculate
- No runaway money snowball

**Cons:**
- Flat economy means passive income dominates
- The Draw/+10 choice becomes even more about hand management than money

**Assessment:** Works, but doesn't solve the core problem

---

### Option B: Passive Percentage Interest on Total Budget
**Example: 5% interest on current budget each turn**

**Pros:**
- Rewards economic management
- Creates long-term thinking

**Cons:**
- **MAJOR RISK**: Creates exponential money snowball
- Rich players get richer, poor players fall behind
- Starting rich (multiplayer) becomes dominant strategy
- After 10 turns with 5% interest: 15 → 24 → 37 → 55 → 79 → 112...
- By mid-game, money is meaningless

**Assessment: NOT RECOMMENDED** — Too dangerous for a teaching game

---

### Option C: Delayed Investment Reward (Only When Choosing +10 Budget)
**Example: Choosing +10 this turn gives +3 bonus next turn**

**Pros:**
- Makes the +10 choice feel rewarding
- Creates a small "investment" mechanic
- Doesn't help rich players (they don't need +10)
- Punishes impulsive players who always draw cards
- Maintains simplicity

**Cons:**
- Adds slight complexity
- Small bonus might still not be enough to change behavior

**Assessment: RECOMMENDED** — Safest interest mechanic

---

## 4. Tuning Levers (Priority Order)

### Primary Levers (Most Impact):

1. **Passive Turn Growth** — Start here
   - Currently +5, this is the biggest driver
   - Reducing this makes every decision matter more

2. **Starting Budget** — Secondary
   - Currently 15, affects early-game feel
   - Lower starting = more pressure in first few turns

3. **Bonus Funding Frequency** — Tertiary
   - Currently 1 in 19, quite common
   - Rarer = more exciting when it appears

### Secondary Levers (Fine-tuning):

4. **+10 Budget Option** — Tied to investment mechanic
   - Should feel rewarding when chosen
   - But not so rewarding it becomes the default

5. **Card Costs** — Last resort
   - Changing costs affects gameplay feel
   - Better to adjust economy than card balance

---

## 5. Three Candidate Balance Setups

### Setup A: Mild Tightening
*Keeps most current feel, removes obvious excess*

| Parameter | Current | New Value | Rationale |
|-----------|---------|-----------|-----------|
| Starting Budget | 15 | 12 | Slightly tighter early game |
| Passive Turn Growth | +5 | +4 | Small reduction |
| Bonus Funding Rarity | 1 in 19 | 1 in 25 | Slightly rarer |
| Bonus Funding Reward | +15 | +12 | Slightly smaller |
| Interest Mechanic | None | +2 next turn if choose +10 | Small reward for +10 choice |

**Expected behavior:**
- Players still comfortable, but occasionally feel budget pressure
- +10 becomes a viable early-game choice
- ~25% of turns should involve real budget tension

---

### Setup B: Moderate Tightening (Recommended)
*Creates genuine economy tension while maintaining fun*

| Parameter | Current | New Value | Rationale |
|-----------|---------|-----------|-----------|
| Starting Budget | 15 | 10 | Meaningful early-game decisions |
| Passive Turn Growth | +5 | +3 | Forces prioritization |
| Bonus Funding Rarity | 1 in 19 | 1 in 30 | Rare treat, not regular occurrence |
| Bonus Funding Reward | +15 | +10 | Still impactful, but not game-breaking |
| Interest Mechanic | None | +3 next turn if choose +10 | Makes +10 feel like "investing" |

**Expected behavior:**
- First 3 turns: Real budget pressure, players choose carefully
- Mid-game: Balance between drawing and gaining
- +10 becomes an actual strategic choice, not a hand-full fallback
- ~40-50% of turns should require genuine budget prioritization
- Bonus Funding feels exciting when drawn

---

### Setup C: Strong Tightening
*For experienced players or shorter games*

| Parameter | Current | New Value | Rationale |
|-----------|---------|-----------|-----------|
| Starting Budget | 15 | 8 | High pressure from turn 1 |
| Passive Turn Growth | +5 | +2 | Very limited income |
| Bonus Funding Rarity | 1 in 19 | 1 in 40 | Almost mythical when it appears |
| Bonus Funding Reward | +15 | +15 (keep) | But rare enough to be special |
| Interest Mechanic | None | +4 next turn if choose +10 | Strong incentive for investment |

**Expected behavior:**
- Every turn requires careful budget management
- Players often can only afford 1-2 cards
- Drawing cards feels risky (lost income)
- +10 becomes the default choice early, unless you need a specific card
- Budget becomes a genuine resource, not spare change

---

## 6. Interest Mechanism Design (Safest Version)

### Recommended: Delayed Investment Reward

```javascript
// When a player chooses +10 budget:
player.investmentBonus = 3; // Stored, applied next turn

// At start of each player's turn:
if (player.investmentBonus > 0) {
    player.budget += player.investmentBonus;
    player.investmentBonus = 0; // Reset
}
```

### Why This Is Safe:
1. **Caps naturally** — Only triggers when choosing +10, which means you needed money
2. **Fixed bonus** — No exponential growth, just +3-4 per trigger
3. **Delayed gratification** — Doesn't help immediately, rewards planning
4. **Reduces inequality** — Poor players benefit most (they choose +10 more often)
5. **Simple to understand** — "Choose +10 now, get +3 bonus next turn"

### Alternative: Stacked Investment
If you want to encourage consecutive +10 choices:
```javascript
// Track consecutive +10 choices:
player.consecutiveBudgetChoices++;
if (player.consecutiveBudgetChoices >= 2) {
    player.budget += 5; // Bonus on top of normal gain
    player.consecutiveBudgetChoices = 0; // Reset
}
```

This rewards "sticking with" budget choice, but adds complexity.

---

## 7. Target Frequency for +10 Budget Choice

**Current state:** ~5-10% of turns (only when hand is full)

**Target state:** ~30-40% of turns

### How to Achieve This Target:
1. **Reduce passive income** (most effective)
2. **Make specific cards more expensive** (DDT at 12 is already high)
3. **Add investment bonus** to make +10 feel rewarding
4. **Keep starting budget moderate** (10 is better than 8)

### Success Metric:
Players should occasionally think: "Do I have enough for this card? Should I just take the +10?"

Not every turn. But often enough that the choice matters.

---

## 8. Summary Recommendations

### Primary Recommendation: **Setup B (Moderate Tightening)**

| Parameter | Recommended Value |
|-----------|-------------------|
| Starting Budget | **10** |
| Passive Turn Growth | **+3** |
| Bonus Funding Rarity | **1 in 30** |
| Bonus Funding Reward | **+10** |
| Interest Mechanic | **+3 next turn** (only if choose +10) |

### Why This Works:
1. **Early game is tight** — 10 budget means first 3 turns require real choices
2. **Middle game is balanced** — +3 passive keeps you alive, but +10 is valuable
3. **Late game has events** — WHO Funding provides relief, but not guaranteed
4. **Investment matters** — Choosing +10 now gives +3 bonus next turn, stacking appeal
5. **Bonus Funding is exciting** — Rarer makes it feel like a lucky find

### Secondary Recommendation:
If the game feels **too** tight after implementation, the first adjustment should be passive growth (+3 → +4), not reducing rarity or starting budget.

---

## 9. Optional Advanced: Hand Size Consideration

Currently: Max 6 cards

If economy is still too loose after Setup B:
- Reduce hand size to **5 cards**
- This forces more Draw/+10 choices (hand fills faster)
- More natural budget pressure without changing numbers

This is a **secondary lever** — try economy adjustments first.
