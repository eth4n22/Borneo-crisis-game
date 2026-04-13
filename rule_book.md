# Fixes That Fail: The Borneo Simulation
## Rule Book

---

## Table of Contents

1. [Overview](#1-overview)
2. [Objective](#2-objective)
3. [Game Structure](#3-game-structure)
4. [How to Play](#4-how-to-play)
5. [Game Variables](#5-game-variables)
6. [Card System](#6-card-system)
7. [Natural Dynamics](#7-natural-dynamics)
8. [Harm and Health](#8-harm-and-health)
9. [Trust and Compliance](#9-trust-and-compliance)
10. [Global Events](#10-global-events)
11. [Scoring](#11-scoring)
12. [Ecological Chain (Hidden)](#12-ecological-chain-hidden)
13. [Special Thresholds](#13-special-thresholds)

---

## 1. Overview

**Fixes That Fail: The Borneo Simulation** is a turn-based multiplayer policy game based on the 1950s Borneo DDT spraying campaign. Players manage disease control across 12 turns (representing 24 months), making strategic decisions that affect mosquitoes, cats, rats, malaria, rat disease, and more.

The game simulates the unintended consequences of well-intentioned policies, teaching players about complex systems, feedback loops, and unintended consequences.

**Players:** 2-4 players  
**Duration:** 12 turns per player (~20-30 minutes)

---

## 2. Objective

Your goal is to achieve the **highest score (0-100)** by managing disease control while protecting your village.

**Balanced Goals:**
- Control malaria and plague
- Maintain village health
- Manage your budget
- Protect housing (roof) and food (grain)
- Maintain public trust

---

## 3. Game Structure

| Parameter | Value |
|-----------|-------|
| Total Turns per Player | 12 |
| Each Turn Represents | 2 months |
| Total Game Duration | 24 months |
| Number of Players | 2-4 (configurable) |
| Turns per Round | Equal to player count |
| **Loss Condition** | Village Health ≤ 0 |

---

## 4. How to Play

### Turn Flow

Each turn follows this structure:

1. **Start of Turn**
   - Budget automatically grows by +3
   - If you chose "+10 Budget" last turn, you receive a +3 investment bonus
   - 1 card is automatically drawn (if your hand has room)

2. **Choose One**
   - **Draw a Card:** Add 1 random card to your hand (if hand < 6)
   - **Gain +10 Budget:** Add +10 to your budget immediately
   - You cannot do both — choose one per turn

3. **Play Cards**
   - Click cards in your hand to select them for play
   - Selected cards are queued to be played
   - Only affordable cards (enough budget) can be selected
   - Multiple cards can be selected to play in the same turn

4. **Confirm Actions**
   - Press "Confirm Actions" to execute all selected cards
   - Budget is deducted for each card
   - Card effects are applied

5. **Next Player**
   - Press "Next Player" to pass to the next player
   - Continue until all players have taken their turn

---

## 5. Game Variables

### Starting Values

| Variable | Starting Value | Description |
|----------|----------------|-------------|
| Mosquitoes | 70 | Disease vectors |
| DDT Level | 0 | Pesticide accumulation |
| Cats | 60 | Natural rat predators |
| Rats | 20 | Disease carriers |
| Malaria Infected | 50 | Malaria burden |
| Rat Disease Infected | 10 | Plague burden |
| Public Trust | 70 | Community confidence |
| Budget | 10 | Resources for actions |
| Village Health | 100 | Primary survival metric |
| Roof Integrity | 80 | Housing protection |
| Grain Stores | 85 | Food supply |
| Wasps | 55 | Caterpillar control |
| Caterpillars | 15 | Roof damage pests |
| Gecko Toxicity | 0 | Hidden DDT accumulation |

### Variable Ranges

| Variable | Min | Max | Visible |
|----------|-----|-----|---------|
| Mosquitoes | 0 | 100 | Yes |
| DDT Level | 0 | 100 | Yes |
| Cats | 0 | No cap | Yes |
| Rats | 0 | No cap | Yes |
| Malaria Infected | 0 | 100 | Yes |
| Rat Disease Infected | 0 | 100 | Yes |
| Public Trust | 0 | 100 | Yes |
| Village Health | 0 | 100 | Yes |
| Roof Integrity | 0 | 100 | Yes |
| Grain Stores | 0 | 100 | Yes |
| Wasps | 0 | 100 | Hidden |
| Caterpillars | 0 | 100 | Hidden |
| Gecko Toxicity | 0 | 100 | Hidden |

---

## 6. Card System

### Card Types

| Card | Cost | Effect |
|------|------|--------|
| 🐱 Cat Drop | 15 | Adds cats to hunt rats (delayed 1 turn) |
| 💧 Spray DDT | 12 | Reduces mosquitoes instantly |
| 📢 Awareness Campaign | 10 | Boosts trust, reduces malaria and plague |
| 🏚️ Repair Thatch | 8 | Restores roof integrity |
| 🌾 Protect Grain | 6 | Restores grain stores |
| 🏥 Medical Aid | 5 | Reduces both malaria and plague |
| 💰 Bonus Funding | Free | Adds +15 budget (rare) |

### Card Costs Detail

| Card | Cost | Notes |
|------|------|-------|
| Spray DDT | 12 | Affects ecological chain |
| Cat Drop | 15 | Delayed 1 turn |
| Awareness | 10 | Has delayed effects |
| Repair Thatch | 8 | Instant |
| Protect Grain | 6 | Instant |
| Medical Aid | 5 | Instant |
| Bonus Funding | 0 | Rare (1 in 25 chance to draw) |

### Card Effects Detail

**Spray DDT (Cost: 12)**
- Mosquitoes: -18 (affected by compliance)
- DDT Level: +20
- Trust: +2
- Delayed: Adds to ecological damage queue
- Warning: Using 3 times in a row causes trust fatigue (-8)

**Cat Drop (Cost: 15)**
- Cats: +18 (or +9 if cats > 85)
- Rats: -10 (delayed 1 turn)
- Trust: +1

**Awareness Campaign (Cost: 10)**
- Trust: +1 immediate
- Malaria: -3 immediate
- Plague: -3 immediate
- Delayed (1 turn): Mosquitoes -4, Rats -4

**Repair Thatch (Cost: 8)**
- Roof Integrity: +10 immediate
- Max roof: 80

**Protect Grain (Cost: 6)**
- Grain Stores: +10 immediate
- Max grain: 85

**Medical Aid (Cost: 5)**
- Malaria: -6 immediate
- Plague: -6 immediate

**Bonus Funding (Cost: Free)**
- Budget: +15 immediate
- Rare: Only appears 1 in 25 draws

### Hand Rules
- Maximum hand size: **6 cards**
- Starting hand: 2 cards (dealt at game start)
- Starting budget: **10**
- Start-of-turn budget growth: **+3 flat**
- Investment bonus: **+3** on next turn if you chose "+10 Budget"

---

## 7. Natural Dynamics

Every turn, the following changes happen **automatically** before player actions:

### Basic Dynamics

```
Mosquitoes → drift toward 60
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

### Resource Dynamics

```
Roof Integrity → damaged by caterpillars
    caterpillar_damage = floor(caterpillars / 12)
    roof_integrity = max(0, min(100, roof_integrity - caterpillar_damage))

Grain Stores → consumed + rat damage
    grain_loss = 2 + floor(rats / 18)
    grain_stores = max(0, min(100, grain_stores - grain_loss))
```

### Hidden Ecological Chain

```
Wasps → decline from DDT, recover slowly
    wasps -= floor(ddt / 10)
    wasps += 1

Caterpillars → grow when wasps are low
    if wasps < 30:
        caterpillars += 2 + floor((30 - wasps) / 10)
    else:
        caterpillars += 1

Gecko Toxicity → builds from DDT (hidden)
    if ddt > 5:
        gecko_toxicity += floor(ddt / 20) + 1
    else:
        gecko_toxicity -= 1

Cats → die from eating toxic geckos (hidden pathway)
    if gecko_toxicity > 35:
        cats -= floor((gecko_toxicity - 35) / 15) + 1
```

---

## 8. Harm and Health

### Harm Types

| Harm | Condition | Formula |
|------|-----------|---------|
| Malaria Harm | Always | floor(malaria_infected / 15) |
| Plague Harm | Always | floor(rat_disease_infected / 12) |
| Starvation Harm | Only when grain < 50 | floor((50 - grain) / 8) + 1 |
| Exposure Harm | Only when roof < 50 | floor((50 - roof) / 8) + 1 |

**Examples:**
- Grain 49 → Starvation 1
- Grain 40 → Starvation 1
- Grain 30 → Starvation 2
- Grain 20 → Starvation 3
- Roof 49 → Exposure 1
- Roof 40 → Exposure 2
- Roof 30 → Exposure 3

### Village Health Calculation

```
Village Health = previous_health 
    - malaria_harm 
    - plague_harm 
    - starvation_harm 
    - exposure_harm
```

### Deaths

```
Deaths This Turn = malaria_harm + plague_harm + starvation_harm + exposure_harm
Deaths Cumulative += deaths_this_turn
```

### Health Status Thresholds

| Health Range | Status | Color |
|--------------|--------|-------|
| ≥ 70% | Thriving | 🟢 Green |
| 40-69% | Struggling | 🟠 Orange |
| < 40% | Collapsing! | 🔴 Red |

---

## 9. Trust and Compliance

### Trust Effects

| Condition | Effect |
|-----------|--------|
| Trust < 25 | Compliance = 0.7 (actions 30% less effective) |
| Trust ≥ 25 | Compliance = 1.0 (full effectiveness) |
| Budget < 0 | Budget = 0; Actions continue normally |
| **Trust ≥ 70** | Village Health +1 resilience recovery each turn |
| Trust Decay | -1 each turn automatically |

### Trust from Events

| Event | Trust Change |
|-------|--------------|
| Deaths ≥ 5 | -floor(deaths / 3) |
| Deaths < 5 | +2 |
| DDT 3× in a row | -8 (fatigue) |
| Global Scandal | -4 to -10 |

---

## 10. Global Events

### Trigger Timing
- After all players complete one full rotation (end of round)
- 30% chance of random event (excluding guaranteed funding)

### Guaranteed WHO Funding
- Determined at game start
- 60% chance: Round 7
- 30% chance: Round 8
- 10% chance: Round 9
- Takes priority over random events
- Effect: **Budget +25**, **Trust +3** (applied to ALL players)

### Random Events

| Event | Chance | Effects |
|-------|--------|---------|
| 🌧️ Monsoon | 10% | Mosquitoes +15, Malaria +5, Trust -2 |
| 🐀 Rodent Migration | 6% | Rats +20, Rat Disease +5, Cats -3 |
| 📰 DDT Scandal | 5% | Trust -10 (if DDT>40), else -4 |
| 🧹 Clean-Up | 5% | Rats -10, Rat Disease -5, Trust +6 |
| 🐱 Cat Disease | 4% | Cats -15, Rats +8, Trust -3 |

---

## 11. Scoring

### Penalty Calculation

| Penalty | Formula | Max Penalty |
|---------|---------|-------------|
| Deaths | min(60, deaths × 0.6) | -60 |
| Peak Infection | min(20, peak × 0.1) | -20 |
| Duration | min(15, outbreak × 1.5) | -15 |
| Eco Collapse | min(20, events × 5) | -20 |
| Roof Collapse | 5 if final_roof < 30 | -5 |
| Grain Collapse | 5 if final_grain < 30 | -5 |

### Bonus Calculation

| Bonus | Formula | Max Bonus |
|-------|---------|-----------|
| Trust | min(10, floor(final_trust / 10)) | +10 |
| Budget | min(5, floor(final_budget / 20)) | +5 |

### Final Score Formula

```
Score = max(0, min(100, 
    100 
    - deaths_penalty 
    - peak_penalty 
    - duration_penalty 
    - eco_penalty 
    - roof_collapse_penalty 
    - grain_collapse_penalty
    + trust_bonus 
    + budget_bonus
))
```

### Tie-Breakers (in order)
1. **Fewer deaths** — lower is better
2. **Lower peak infection** — lower is better
3. **Shorter outbreak duration** — lower is better
4. **Higher final trust** — higher is better
5. **Higher final budget** — higher is better

---

## 12. Ecological Chain (Hidden)

Version 2B introduces hidden ecological consequences that players discover through gameplay.

### The Hidden Chain

```
DDT → harms wasps → caterpillars multiply → roof damage
DDT → accumulates in geckos → cats eat toxic geckos → cats die → rats explode
```

### Visible Warnings

Watch for these messages in the event log:
- "Wasp populations declining from pesticide exposure"
- "Caterpillars multiplying as their predators disappear"
- "Caterpillars are damaging the thatch roof"
- "Toxicity is building in the food chain..."
- "Cats are becoming sick after consuming contaminated prey"

### Ecological Collapse

**Definition:** When `cats < 20` AND `rats > 80`

Each occurrence:
- Increments eco_events counter
- -5 points to score (max -20)

---

## 13. Special Thresholds

| Threshold | Condition | Effect |
|-----------|-----------|--------|
| Cats > 85 | Cat Drop | Increase halved to +9 |
| Grain < 50 | Each Turn | Starvation harm begins |
| Roof < 50 | Each Turn | Exposure harm begins |
| Village Health ≤ 0 | Each Turn | Player collapses (game over for them) |
| Hand = 6 | Auto-draw | No auto-draw (hand full) |
| DDT > 40 | Scandal Event | Trust -10 (vs -4) |
| Trust < 25 | Any Action | 30% effectiveness reduction |
| Consecutive DDT ≥ 3 | DDT Action | Trust -8 (fatigue) |
| Malaria + Plague > 60 | Each Turn | Counts toward outbreak duration |

---

## Quick Reference

### Card Quick Guide

| Icon | Action | Cost | Key Effect |
|------|--------|------|------------|
| 💧 | Spray DDT | 12 | Fast mosquito reduction |
| 🐱 | Cat Drop | 15 | Long-term rat control |
| 📢 | Awareness | 10 | All-around support |
| 🏚️ | Repair Thatch | 8 | Protect from exposure |
| 🌾 | Protect Grain | 6 | Protect from starvation |
| 🏥 | Medical Aid | 5 | Quick disease relief |
| 💰 | Bonus Funding | 0 | Free money (rare) |

### Turn Summary

1. +3 budget (automatic)
2. +3 investment bonus (if chose +10 last turn)
3. Auto-draw 1 card
4. Choose: +10 budget OR draw extra card
5. Select and play cards
6. Confirm actions
7. Next player

---

*Rule Book v1.0 - Based on game version 2B*