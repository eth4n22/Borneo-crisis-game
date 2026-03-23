# DDT Decision: Borneo Crisis
## Model Documentation

---

## Overview

DDT Decision: Borneo Crisis is a turn-based systems-thinking policy game simulating the 1950s Borneo DDT spraying campaign. Players manage disease control across 12 turns (representing 24 months), making decisions that affect mosquitoes, cats, rats, malaria, and rat disease levels.

**Version 2A Update:** Added housing/resource survival layer with Village Health, Roof Integrity, Grain Stores, and a new Repair Thatch action.

**Version 2B Update:** Added hidden ecological chain mechanics (wasps, caterpillars, gecko toxicity) and replaced direct action selection with card-based action economy system.

### Key Feedback Loops

| Loop | Type | Description |
|------|------|-------------|
| **B-Loop 1** | Balancing | DDT → mosquitoes ↓ → malaria ↓ |
| **B-Loop 2** | Balancing | Cat Drop → cats ↑ → rats ↓ → rat disease ↓ |
| **R-Loop** | Reinforcing | DDT → cats ↓ → rats ↑ → rat disease ↑ *(unintended!)* |
| **B-Loop 3 (V2A)** | Balancing | Repair Thatch → roof ↑ → exposure ↓ → village health ↑ |
| **R-Loop 2 (V2A)** | Reinforcing | Rats ↑ → grain ↓ → starvation ↑ → village health ↓ |
| **B-Loop 4 (V2B)** | Balancing | Wasps → caterpillars ↓ → roof ↓ *(hidden chain)* |
| **R-Loop 3 (V2B)** | Reinforcing | DDT → gecko toxicity ↑ → cats ↓ → rats ↑ *(hidden chain)* |

---

## 1. Initial Values

| Variable | Starting Value |
|----------|-----------------|
| Turn | 1 |
| Mosquitoes | 70 |
| DDT Level | 0 |
| Cats | 60 |
| Rats | 20 |
| Malaria Infected | 50 |
| Rat Disease Infected | 10 |
| Deaths (Cumulative) | 0 |
| Deaths This Turn | 0 |
| Public Trust | 70 |
| **Budget** | **10** |
| **Village Health (V2A)** | 100 |
| **Roof Integrity (V2A)** | 80 |
| **Grain Stores (V2A)** | 85 |
| **Wasps (V2B)** | 55 |
| **Caterpillars (V2B)** | 15 |
| **Gecko Toxicity (V2B)** | 0 (hidden) |

### Delay Queues (Initial)

| Queue | Initial Values |
|-------|----------------|
| Deaths Queue | `[0, 0]` (length: 2) |
| DDT Ecology Queue | `[0, 0]` (length: 2) |
| Cat Drop Queue | `[0]` (length: 1) |
| Awareness Queue (V2A) | `[0]` (length: 1) |

---

## 2. Game Structure

| Parameter | Value |
|-----------|-------|
| Total Turns per Player | 12 |
| Each Turn Represents | 2 months |
| Total Game Duration | 24 months |
| Number of Players | 2-4 (configurable) |
| Turns per Round | Equal to player count |
| **Loss Condition (V2A)** | Village Health ≤ 0 or turn > 12 |

---

## 3. Card-Based Action System (Version 2B)

The game uses a card-based action economy system where players build a hand of action cards.

### Turn Flow

**Step 1 - Start of Turn:**
- Budget grows by +3 (flat, no interest)
- 1 card is automatically drawn (if hand < 6)
- Hand is always visible

**Step 2 - Choose One:**
- **Draw a Card:** Add 1 random card to hand (if hand < 6)
- **Add Budget:** Gain +10 budget immediately
- Cannot do both - choose one per turn

**Step 3 - Select Cards:**
- Click cards in hand to select/deselect for play
- Selected cards are highlighted
- Only affordable cards can be selected
- Can select multiple cards to play in same turn

**Step 4 - End Turn:**
- Click "End Turn" to execute all selected cards
- Budget is deducted for each card
- Card effects are applied
- Turn passes to next player

### Card Types (7 types)

| Card | Cost | Effect |
|------|------|--------|
| Cat Drop | 15 | Cats +18 (or +9 if cats > 85), Rats -10, Trust +1 |
| Spray DDT | 12 | Mosquitoes -18, DDT +20, Trust +2 |
| Awareness | 10 | Trust +8, Malaria -3, Rat Disease -3, Delayed: Mosquitoes -4, Rats -4 |
| Repair Thatch | 8 | Roof +20, Trust +1 |
| Protect Grain | 6 | Grain +12, Trust +1 |
| Bonus Funding | 0 | Budget +15 |
| Medical Aid | 0 | Malaria -8 |

### Hand Rules
- Maximum hand size: 6 cards
- Starting hand: 0 cards (auto-draw 1 at start of each turn)
- **Starting budget: 10**
- Start-of-turn budget growth: +3 flat
- Investment bonus: +3 on next turn if +10 budget chosen

---

## 4. Natural Dynamics (Each Turn)

The following changes happen **automatically** every turn, before player actions:

```
Mosquitoes → drifts toward 60
    mosquitoes += round((60 - mosquitoes) × 0.10)

DDT → decays
    ddt = max(0, ddt - 8)

Cats → stressed by rats
    cats += 1 - floor(rats / 25)

Rats → reproduce based on cat presence
    rats += 4 + floor((50 - cats) / 20)

Malaria → follows mosquitoes
    malaria_infected += floor((mosquitoes - malaria_infected) × 0.25)

Rat Disease → follows rats
    rat_disease_infected += floor((rats - rat_disease_infected) × 0.20)
```

**Version 2A: Resource Dynamics**
```
Roof Integrity → damaged by caterpillars (V2B: no passive decay)
    caterpillar_damage = floor(caterpillars / 12)
    roof_integrity = max(0, min(100, roof_integrity - caterpillar_damage))

Grain Stores → consumed + rat damage (V2B adjusted)
    grain_loss = 2 + floor(rats / 18)
    grain_stores = max(0, min(100, grain_stores - grain_loss))
```

**Version 2B: Ecological Chain (Hidden)**
```
Wasps → decline from DDT, recover slowly
    wasps -= floor(ddt / 10)
    wasps += 1

Caterpillars → grow when wasps are low
    if wasps < 30:
        caterpillars += 2 + floor((30 - wasps) / 10)
    else:
        caterpillars += 1

Gecko Toxicity → builds from DDT (hidden variable)
    if ddt > 5:
        gecko_toxicity += floor(ddt / 20) + 1
    else:
        gecko_toxicity -= 1

Cats → die from eating toxic geckos (hidden pathway)
    if gecko_toxicity > 35:
        cats -= floor((gecko_toxicity - 35) / 15) + 1
```

All variables are clamped after each turn:
- Mosquitoes, DDT, Malaria, Rat Disease, Public Trust, Wasps, Caterpillars: clamped to **0-100**
- Village Health, Roof Integrity, Grain Stores: clamped to **0-100**
- Gecko Toxicity: clamped to **0-100** (hidden)
- Cats, Rats: **no longer clamped** - can grow beyond 100 based on game dynamics

---

## 5. Village Health & Deaths (Version 2A)

Village Health is now the primary survival metric, computed as a STOCK (not recalculated from 100 each turn):

```
MALARIA HARM = floor(malaria_infected / 25)
PLAGUE HARM = floor(rat_disease_infected / 20)

STARVATION HARM (only when grain < 50):
    = floor((40 - grain_stores) / 10) + 1
    Examples: grain 49→1, grain 40→1, grain 30→2, grain 20→3

EXPOSURE HARM (only when roof < 50):
    = floor((50 - roof_integrity) / 10) + 1
    Examples: roof 49→1, roof 40→2, roof 30→3

RESILIENCE RECOVERY:
    = 1 if public_trust >= 70, else 0

VILLAGE HEALTH = previous_health - malaria_harm - plague_harm - starvation_harm - exposure_harm + resilience_recovery
    (clamped to 0-100)

DEATHS THIS TURN = malaria_harm + plague_harm + starvation_harm + exposure_harm
DEATHS CUMULATIVE += deaths_this_turn
```

---

## 6. Player Actions

### A) Cat Drop
| Effect | Value |
|--------|-------|
| Cost | 15 budget |
| Trust | +1 |
| Delayed (1 turn) | Cats +18 (or +9 if cats > 85) |
| | Rats -10 |

### B) Spray DDT
| Effect | Value |
|--------|-------|
| Cost | 12 budget |
| Mosquitoes | -floor(18 × compliance) |
| DDT | +20 |
| Trust | +2 |
| Repeated Use (3×) | Trust -8 |

### C) Awareness Campaign (V2A)
| Effect | Value |
|--------|-------|
| Cost | 10 budget |
| Trust | +8 |
| Malaria | -3 immediate |
| Rat Disease | -3 immediate |
| Delayed (1 turn) | Mosquitoes -4, Rats -4 |

### D) Repair Thatch (V2A)
| Effect | Value |
|--------|-------|
| Cost | 8 budget |
| Trust | +1 |
| Roof Integrity | +20 immediate |
| No delayed effects | - |

### E) Protect Grain (V2A.1)
| Effect | Value |
|--------|-------|
| Cost | 6 budget |
| Trust | +1 |
| Grain Stores | +12 immediate |
| No delayed effects | - |

### F) Do Nothing
| Effect | Value |
|--------|-------|
| Cost | Free |
| Trust | -3 |
| Mosquitoes | +5 |

---

## 7. Trust & Compliance

| Condition | Effect |
|-----------|--------|
| Trust < 25 | Compliance = 0.7 (actions 30% less effective) |
| Trust ≥ 25 | Compliance = 1.0 (full effectiveness) |
| Budget < 0 | Budget = 0; Actions Disabled |
| Budget ≥ 10 | Actions Re-enabled |
| Deaths ≥ 5 | Trust -= floor(deaths / 3) |
| Deaths < 5 | Trust += 2 |
| **Trust ≥ 70** | Village Health +1 resilience recovery |

---

## 8. Village Health Meter (Version 2A)

The health meter now displays **Village Health** as the primary survival metric.

### Thresholds (V2A)

| Health Range | Label | Color |
|--------------|-------|-------|
| ≥ 70% | Thriving | 🟢 Green |
| 40-69% | Struggling | 🟠 Orange |
| < 40% | Collapsing! | 🔴 Red |

### Game End Conditions
- **Turn limit:** Player finishes after turn 12
- **Collapse:** If Village Health ≤ 0, player is marked as collapsed

---

## 9. Scoring System

### Penalties

| Penalty | Formula | Max |
|---------|---------|-----|
| Deaths | min(60, deaths × 0.6) | -60 |
| Peak Infection | min(20, peak × 0.1) | -20 |
| Duration | min(15, outbreak × 1.5) | -15 |
| Eco Collapse | min(20, events × 5) | -20 |
| **Roof Collapse** | 5 if final_roof < 30 | -5 |
| **Grain Collapse** | 5 if final_grain < 30 | -5 |

### Bonuses

| Bonus | Formula | Max |
|-------|---------|-----|
| Trust | min(10, floor(final_trust / 10)) | +10 |
| Budget | min(5, floor(final_budget / 20)) | +5 |

### Final Score
```
Score = max(0, min(100, 
    100 - deaths_penalty - peak_penalty - duration_penalty - eco_penalty 
    - roof_collapse_penalty - grain_collapse_penalty
    + trust_bonus + budget_bonus
))
```

### Tie-Breakers (in order)
1. Fewer deaths
2. Lower peak infection
3. Higher final trust
4. **Higher final village health**

---

## 10. Global Events

### Probability
- **No Event:** 70%
- **Random Event:** 30%

### Guaranteed WHO Funding
- Determined at game start
- 60% chance: Round 7
- 30% chance: Round 8
- 10% chance: Round 9
- Takes priority over random events
- Effect: Budget +25 (unbounded), Trust +3

### Random Events (30% total)

| Event | Probability | Effects |
|-------|-------------|---------|
| 🌧️ Monsoon | 10% | Mosquitoes +15, Malaria +5, Trust -2 |
| 🐀 Rodent Migration | 6% | Rats +20, Rat Disease +5, Cats -3 |
| 📰 DDT Scandal | 5% | Trust -10 (if DDT>40), else -4 |
| 🧹 Clean-Up | 5% | Rats -10, Rat Disease -5, Trust +6 |
| 🐱 Cat Disease | 4% | Cats -15, Rats +8, Trust -3 |

---

## 11. Special Thresholds

| Threshold | Condition | Effect |
|-----------|-----------|--------|
| Cats > 85 | Cat Drop | Increase halved to 9 |
| Rats < 20 | Rat Campaign | Reduction limited to 6 |
| DDT > 40 | Scandal Event | Trust -10 (vs -4) |
| Trust < 25 | Any Action | 30% effectiveness reduction |
| Budget < 0 | End of Turn | Budget=0, Actions Disabled |
| Consecutive DDT ≥ 3 | DDT Action | Trust -8 (fatigue) |
| Malaria + Rat Disease > 60 | Each Turn | Counts toward outbreak duration |
| **Grain < 50** | Each Turn | Starvation harm begins |
| **Roof < 50** | Each Turn | Exposure harm begins |
| **Village Health ≤ 0** | Each Turn | Player collapses |

---

## 12. Eco Collapse

**Definition:** When `cats < 20` AND `rats > 80`

Each occurrence:
- Increments eco_events counter
- -5 points to score (max -20)

---

## 13. Version 2B: Hidden Ecological Chain

Version 2B introduces hidden ecological consequences that players discover through gameplay:

### Hidden Variables (not displayed)
- **Gecko Toxicity:** Accumulates when DDT is used, builds up in the food chain
- Starts at 0, accumulates based on DDT level

### Hidden Pathways
1. **DDT → Wasps:** DDT kills beneficial wasps that control caterpillars
2. **Wasps → Caterpillars:** Fewer wasps = more caterpillars
3. **Caterpillars → Roof:** Caterpillars eat thatch, damaging roofs (VISIBLE: roof damage shown)
4. **DDT → Gecko Toxicity:** DDT accumulates in gecko tissue through bioaccumulation
5. **Gecko Toxicity → Cats:** Cats eat toxic geckos and die (VISIBLE: cat deaths shown)

### Player Discovery
- Roof damage warnings appear when caterpillars increase
- Cat death warnings appear when gecko toxicity is high
- Event log messages reveal the hidden chain:
  - "Wasp populations declining from pesticide exposure"
  - "Caterpillars multiplying as their predators disappear"
  - "Toxicity is building in the food chain..."

---

## 14. Version History

| Version | Changes |
|---------|---------|
| **V2A** | Added Village Health, Roof Integrity, Grain Stores, Repair Thatch, Protect Grain actions |
| **V2A.1** | Adjusted grain loss formula, buffed resilience to +2 |
| **V2B** | Added card-based economy system, wasps, caterpillars, gecko toxicity (hidden), ecological chain |

---

*Documentation generated from Borneo Crisis game code - Version 2B*
