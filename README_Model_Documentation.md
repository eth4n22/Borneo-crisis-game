# DDT Decision: Borneo Crisis
## Model Documentation

---

## Overview

DDT Decision: Borneo Crisis is a turn-based systems-thinking policy game simulating the 1950s Borneo DDT spraying campaign. Players manage disease control across 12 turns (representing 24 months), making decisions that affect mosquitoes, cats, rats, malaria, and rat disease levels.

### Key Feedback Loops

| Loop | Type | Description |
|------|------|-------------|
| **B-Loop 1** | Balancing | DDT → mosquitoes ↓ → malaria ↓ |
| **B-Loop 2** | Balancing | Cat Drop → cats ↑ → rats ↓ → rat disease ↓ |
| **R-Loop** | Reinforcing | DDT → cats ↓ → rats ↑ → rat disease ↑ *(unintended!)* |

---

## 1. Initial Values

| Variable | Starting Value |
|----------|---------------|
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
| Budget | 100 |

### Delay Queues (Initial)

| Queue | Initial Values |
|-------|----------------|
| Deaths Queue | `[0, 0]` (length: 2) |
| DDT Ecology Queue | `[0, 0]` (length: 2) |
| Cat Drop Queue | `[0]` (length: 1) |
| Rat Control Queue | `[0]` (length: 1) |

---

## 2. Game Structure

| Parameter | Value |
|-----------|-------|
| Total Turns per Player | 12 |
| Each Turn Represents | 2 months |
| Total Game Duration | 24 months |
| Number of Players | 2-4 (configurable) |
| Turns per Round | Equal to player count |

---

## 3. Natural Dynamics (Each Turn)

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

All variables are clamped after each turn:
- Mosquitoes, DDT, Malaria, Rat Disease, Public Trust: clamped to **0-100**
- Cats, Rats: **no longer clamped** - can grow beyond 100 based on game dynamics

---

## 4. Deaths Calculation

Deaths are delayed by 1 turn for realism:

```
Immediate Deaths = floor(malaria × 0.10) + floor(rat_disease × 0.12)

Queue: push(immediate) → shift() → apply as deaths_this_turn
Deaths Cumulative += deaths_this_turn
```

---

## 5. Player Actions

### A) Spray DDT
| Effect | Value |
|--------|-------|
| Cost | 12 budget |
| Mosquitoes | -floor(18 × compliance) |
| DDT | +20 |
| Trust | +2 |
| Delayed (2 turns) | Push `floor(ddt/12) + 3` to ecology queue |
| Repeated Use (3×) | Trust -8 |

### B) Do Nothing
| Effect | Value |
|--------|-------|
| Cost | Free |
| Trust | -3 |
| Mosquitoes | +5 |

### C) Cat Drop
| Effect | Value |
|--------|-------|
| Cost | 15 budget |
| Trust | +1 |
| Delayed (1 turn) | Cats +18 (or +9 if cats > 85) |
| | Rats -10 |

### D) Rat Campaign
| Effect | Value |
|--------|-------|
| Cost | 10 budget |
| Trust | +1 |
| Delayed (1 turn) | Rats -16 (or -6 if rats < 20) |
| Side Effect | Cats -3 |
| Side Effect | Trust -2 |

---

## 6. Delay / Queue Logic

| Queue | Length | What Gets Pushed | What Happens When Shifted |
|-------|--------|------------------|--------------------------|
| Deaths | 2 | Immediate deaths | Applied as deaths_this_turn |
| DDT Ecology | 2 | floor(ddt/12) + 3 | cats -= hit; rats += floor(hit/2) |
| Cat Drop | 1 | 18 (or 9 if cats > 85) | cats += actual; rats -= 10 |
| Rat Control | 1 | 16 (or 6 if rats < 20) | rats -= actual; cats -= 3; trust -= 2 |

---

## 7. Trust & Compliance

| Condition | Effect |
|-----------|--------|
| Trust < 25 | Compliance = 0.7 (actions 30% less effective) |
| Trust ≥ 25 | Compliance = 1.0 (full effectiveness) |
| Budget < 0 | Budget = 0; Trust -= 10; Actions Disabled |
| Budget ≥ 10 | Actions Re-enabled |
| Deaths ≥ 5 | Trust -= floor(deaths / 3) |
| Deaths < 5 | Trust += 2 |

---

## 8. Health Meter

### Formula
```
Health = 100 
  - (malaria × 0.4) 
  - (rat_disease × 0.4) 
  - (rats × 0.1) 
  - (ddt × 0.1) 
  - (deaths_this_turn × 2) 
  + (trust × 0.2)
```

Note: Since rats can now exceed 100, the rats × 0.1 contribution is uncapped and can significantly impact health.

Clamped to 0-100, rounded.

### Thresholds

| Health Range | Label | Color |
|--------------|-------|-------|
| ≥ 70% | Healthy | 🟢 Green |
| 30-69% | Strained | 🟠 Orange |
| < 30% | Critical | 🔴 Red |

---

## 9. Scoring System

### Penalties

| Penalty | Formula | Max |
|---------|---------|-----|
| Deaths | min(60, deaths × 0.6) | -60 |
| Peak Infection | min(20, peak × 0.1) | -20 |
| Duration | min(15, outbreak × 1.5) | -15 |
| Eco Collapse | min(20, events × 5) | -20 |

### Bonuses

| Bonus | Formula | Max |
|-------|---------|-----|
| Trust | min(10, floor(final_trust / 10)) | +10 |
| Budget | min(5, floor(final_budget / 20)) | +5 |

### Final Score
```
Score = max(0, min(100, 
    100 - deaths_penalty - peak_penalty - duration_penalty - eco_penalty + trust_bonus + budget_bonus
))
```

### Tie-Breakers (in order)
1. Fewer deaths
2. Lower peak infection
3. Higher final trust

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
| Budget < 0 | End of Turn | Budget=0, Trust-10, Actions Disabled |
| Consecutive DDT ≥ 3 | DDT Action | Trust -8 (fatigue) |
| Malaria + Rat Disease > 60 | Each Turn | Counts toward outbreak duration |

---

## 12. Eco Collapse

**Definition:** When `cats < 20` AND `rats > 80`

Each occurrence:
- Increments eco_events counter
- -5 points to score (max -20)

---

*Documentation generated from Borneo Crisis game code.*
