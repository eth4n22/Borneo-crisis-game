# Balance Diagnosis: Borneo Crisis Version 2B

## Current System Formulas

### Village Health Harm
```
malaria_harm = floor(malaria_infected / 25)
plague_harm = floor(rat_disease_infected / 20)
starvation_harm = floor((40 - grain) / 10) + 1 (only when grain < 40)
exposure_harm = floor((40 - roof) / 12) + 1 (only when roof < 40)
resilience = +1 if trust >= 70

village_health_change = -malaria_harm - plague_harm - starvation_harm - exposure_harm + resilience
```

### Trust System
```
deaths >= 5: trust -= floor(deaths / 3)
deaths < 5: trust += 2
```

### Cat/Rat Dynamics
```
cats += 1 - floor(rats / 25)
rats += 4 + floor((50 - cats) / 20)
```

### Roof Damage (Version 2B)
```
caterpillar_damage = floor(caterpillars / 20)
roof -= caterpillar_damage
```

### Grain Loss
```
grain_loss = 1 + floor(rats / 25)
grain -= grain_loss
```

### Scoring
```
deaths_penalty = min(60, deaths * 0.6)
peak_penalty = min(20, peak_infection * 0.1)
duration_penalty = min(15, outbreak_duration * 1.5)
eco_penalty = min(20, eco_events * 5)
roof_collapse_penalty = 5 if final_roof < 30
grain_collapse_penalty = 5 if final_grain < 30
trust_bonus = min(10, floor(final_trust / 10))
budget_bonus = min(5, floor(final_budget / 20))

score = 100 - penalties + bonuses
```

---

## Playstyle Simulations

### Starting Conditions
- Village Health: 100
- Trust: 70
- Roof: 80
- Grain: 85
- Cats: 60, Rats: 20
- Malaria: 50, Rat Disease: 10
- Mosquitos: 70, DDT: 0
- Wasps: 55, Caterpillars: 15

---

### Playstyle A: Passive Play (Do Nothing Every Turn)

**Turn-by-turn simulation (key values):**

| Turn | Trust | Village | Roof | Grain | Cats | Rats | Malaria | Deaths |
|------|-------|---------|------|-------|------|------|---------|--------|
| 1 | 72 | 100 | 80 | 84 | 60 | 24 | 48 | 0 |
| 2 | 74 | 100 | 80 | 83 | 59 | 28 | 46 | 0 |
| 3 | 76 | 100 | 80 | 82 | 58 | 33 | 43 | 0 |
| 4 | 78 | 100 | 80 | 81 | 57 | 38 | 41 | 0 |
| 5 | 80 | 100 | 80 | 80 | 56 | 43 | 38 | 0 |
| 6 | 82 | 100 | 80 | 79 | 55 | 48 | 36 | 0 |
| 7 | 84 | 100 | 80 | 78 | 54 | 53 | 33 | 0 |
| 8 | 86 | 100 | 80 | 77 | 53 | 58 | 31 | 0 |
| 9 | 88 | 100 | 80 | 76 | 52 | 63 | 28 | 0 |
| 10 | 90 | 100 | 80 | 75 | 51 | 68 | 26 | 0 |
| 11 | 92 | 100 | 80 | 74 | 50 | 73 | 24 | 0 |
| 12 | 94 | 100 | 80 | 73 | 49 | 78 | 21 | 0 |

**Diagnosis:** Village Health NEVER drops. Trust continuously rises. Rats and malaria decline naturally. This playstyle is trivially safe.

**Root Cause:** The natural dynamics favor recovery without intervention. Malaria_infected decreases toward 0 over time. Rat population grows but is kept in check by natural cat recovery.

**Expected Score:** ~90+ (near-perfect due to low deaths and high trust)

---

### Playstyle B: High-Awareness Play (Awareness Every Turn)

**Assumption:** Awareness costs 10, gives Trust +8, Malaria -3, Rat Disease -3

**Turn-by-turn simulation:**

| Turn | Trust | Village | Roof | Grain | Cats | Rats | Malaria | Deaths |
|------|-------|---------|------|-------|------|------|---------|--------|
| 1 | 78 | 100 | 80 | 84 | 60 | 24 | 44 | 0 |
| 2 | 84 | 100 | 80 | 83 | 59 | 28 | 41 | 0 |
| 3 | 90 | 100 | 80 | 82 | 58 | 33 | 38 | 0 |
| 4 | 96 | 100 | 80 | 81 | 57 | 38 | 35 | 0 |
| 5 | 100 | 100 | 80 | 80 | 56 | 43 | 32 | 0 |
| 6 | 100 | 100 | 80 | 79 | 55 | 48 | 29 | 0 |
| 7 | 100 | 100 | 80 | 78 | 54 | 53 | 26 | 0 |
| 8 | 100 | 100 | 80 | 77 | 53 | 58 | 24 | 0 |
| 9 | 100 | 100 | 80 | 76 | 52 | 63 | 22 | 0 |
| 10 | 100 | 100 | 80 | 75 | 51 | 68 | 20 | 0 |

**Diagnosis:** Trust caps at 100. Malaria drops faster. Village Health stays at 100. Game is trivially easy.

**Expected Score:** 95+ (almost perfect)

---

### Playstyle C: DDT-Heavy Play (Spray DDT Every Turn)

**Assumption:** DDT costs 12, gives Mosquitoes -18, DDT +20, Trust +2

**Turn-by-turn simulation (key values):**

| Turn | Trust | Village | Roof | Grain | Cats | Rats | Malaria | DDT | Eco Toxicity | Wasps | Caterpillars |
|------|-------|---------|------|-------|------|------|---------|-----|--------------|-------|--------------|
| 1 | 72 | 100 | 80 | 84 | 60 | 24 | 32 | 20 | 2 | 53 | 16 |
| 2 | 74 | 100 | 80 | 83 | 60 | 24 | 26 | 32 | 4 | 50 | 18 |
| 3 | 76 | 100 | 80 | 82 | 60 | 24 | 21 | 44 | 5 | 46 | 19 |
| 4 | 78 | 100 | 79 | 81 | 60 | 24 | 18 | 56 | 7 | 41 | 21 |
| 5 | 80 | 99 | 79 | 80 | 59 | 26 | 16 | 68 | 8 | 35 | 23 |
| 6 | 82 | 99 | 78 | 79 | 58 | 29 | 14 | 80 | 9 | 27 | 26 |
| 7 | 84 | 98 | 77 | 78 | 57 | 32 | 13 | 92 | 10 | 18 | 29 |
| 8 | 86 | 97 | 75 | 77 | 56 | 36 | 13 | 100 | 11 | 8 | 33 |
| 9 | 88 | 95 | 72 | 76 | 55 | 40 | 15 | 100 | 12 | 0 | 37 |
| 10 | 90 | 92 | 67 | 75 | 54 | 44 | 17 | 100 | 12 | 0 | 41 |
| 11 | 92 | 88 | 60 | 74 | 53 | 48 | 20 | 100 | 12 | 0 | 45 |
| 12 | 94 | 83 | 52 | 73 | 52 | 52 | 23 | 100 | 12 | 0 | 49 |

**Late Game Harm (Turns 10-12):**
- Cat harm from toxic prey begins (DDT > 35)
- Roof damage accelerates as caterpillars grow
- Village Health drops to 83 by turn 12

**Expected Score:** 70-80 (moderate harm from DDT)

---

### Playstyle D: Balanced Play (Mix of Actions)

**Strategy:** Awareness + Cat Drop + occasional Repair

**Turn-by-turn simulation:**

| Turn | Action | Trust | Village | Roof | Grain | Cats | Rats | Malaria |
|------|--------|-------|---------|------|-------|------|------|---------|
| 1 | Awareness | 78 | 100 | 80 | 84 | 60 | 24 | 44 |
| 2 | Cat Drop | 79 | 100 | 80 | 83 | 77 | 14 | 41 |
| 3 | Awareness | 85 | 100 | 80 | 82 | 77 | 14 | 38 |
| 4 | Cat Drop | 86 | 100 | 80 | 81 | 94 | 4 | 35 |
| 5 | Awareness | 92 | 100 | 80 | 80 | 94 | 4 | 32 |
| 6 | Cat Drop | 93 | 100 | 80 | 79 | 100 | 0 | 29 |
| 7 | Awareness | 99 | 100 | 80 | 78 | 100 | 0 | 26 |
| 8 | Repair | 100 | 100 | 100 | 77 | 100 | 0 | 24 |
| 9 | Awareness | 100 | 100 | 100 | 76 | 100 | 0 | 22 |
| 10 | Cat Drop | 100 | 100 | 100 | 75 | 100 | 0 | 20 |

**Diagnosis:** This strategy dominates. Trust caps at 100. Rats eliminated. Village Health at 100. Score ~95+.

---

## System-by-System Balance Review

### 1. Trust System: **TOO FORGIVING**
**Current:** +2 per turn when deaths < 5
**Problem:** Trust continuously rises even with passive play
**Evidence:** Passive play reaches 94 trust by turn 12 with 0 deaths

### 2. Village Health Harm: **TOO WEAK**
**Current:** max harm = 4 (malaria) + 5 (plague) = 9 per turn
**Problem:** At 100 health, this is only 9% damage per turn. Players can survive 11+ turns easily
**Evidence:** Passive play never drops below 100 village health

### 3. Roof Damage (V2B): **TOO SLOW**
**Current:** damage = floor(caterpillars / 20)
**Problem:** Caterpillars need to reach 20+ to deal 1 damage
**With passive play:** Caterpillars only reach ~49 by turn 12, meaning 2 roof damage total
**Evidence:** Passive play roof stays at 80

### 4. Grain/Starvation: **HARMLESS**
**Current:** grain_loss = 1 + floor(rats / 25)
**With passive play:** Rats reach 78, grain_loss = 4.5. Grain drops from 85 to ~73.
**But:** Starvation only triggers when grain < 40. This never happens.
**Evidence:** Passive play grain = 73, starvation = 0

### 5. DDT/Eco Damage: **DELAYED BUT WORKING**
**Current:** Wasps decline, caterpillars grow, eventually roof damage
**Problem:** Takes many turns before effects are felt
**Evidence:** DDT-heavy play shows effects by turn 8-10

### 6. Cat/Rat/Plague: **NATURAL RECOVERY TOO STRONG**
**Current:** cats += 1 - floor(rats / 25)
**Problem:** Even with high rats, cats recover naturally
**With rats = 78:** cats += 1 - 3 = -2 (cats still only decline slightly)
**Evidence:** Passive play cats go from 60 to 49 (decline of 11 over 12 turns)

### 7. Passing/Passivity: **NOT PENALIZED ENOUGH**
**Problem:** Passive play allows trust to rise to 94 while health stays at 100
**Result:** No penalty for inaction

---

## Key Balance Problems Identified

### Problem 1: Trust Recovery is Too Fast
Trust gains +2 per turn with no deaths. This means:
- Passive play continuously increases trust
- By turn 12, trust is near maximum
- The resilience bonus (+1 health) kicks in automatically

### Problem 2: Village Health Harm Thresholds Too High
- Malaria harm = floor(malaria / 25). With malaria at 50, harm = 2
- Plague harm = floor(rat_disease / 20). With rat_disease at 10, harm = 0
- Total max harm: ~4 per turn (only when malaria_infected > 75)

### Problem 3: Roof Damage Too Slow
- caterpillar_damage = floor(caterpillars / 20)
- With starting caterpillars = 15 and growth = 1 per turn, it takes 5 turns to reach 20
- Even then, only 1 damage per turn

### Problem 4: Grain Never Reaches Starvation Threshold
- grain_loss = 1 + floor(rats / 25)
- With rats at 78, grain_loss = 4
- From 85 grain, it takes 11+ turns to reach 40
- Starvation never triggers in passive play

### Problem 5: Natural Recovery Dominates
- Malaria naturally decreases (mosquitoes - malaria_infected relationship)
- Cats naturally recover (even with high rats)
- Rat population growth is capped by natural cat recovery

---

## Recommended Tuning Changes

### Set A: Mild Tuning (Small Adjustments)

**Trust System:**
- Change: +2 → +1 when deaths < 5

**Village Health:**
- Change: malaria_harm = floor(malaria / 20) (was 25)
- Change: plague_harm = floor(rat_disease / 15) (was 20)

**Roof Damage:**
- Change: caterpillar_damage = floor(caterpillars / 15) (was 20)

**Grain:**
- No change (mild concern)

**Impact:** Passive play Village Health drops to ~85-90 instead of 100. Trust rises to ~80 instead of 94.

---

### Set B: Moderate Tuning (Meaningful Pressure)

**Trust System:**
- Change: +2 → +0 when deaths < 5 (no passive gain)
- Add: +1 per turn base trust decay (requires action to maintain)

**Village Health:**
- Change: malaria_harm = floor(malaria / 18)
- Change: plague_harm = floor(rat_disease / 15)
- Change: exposure_harm threshold 40 → 50

**Roof Damage:**
- Change: caterpillar_damage = floor(caterpillars / 12)
- Add: base roof decay = -1 per turn

**Grain:**
- Change: grain_loss = 2 + floor(rats / 20) (was 1 + floor(rats / 25))
- Change: starvation threshold 40 → 45

**Impact:** Passive play Village Health drops to ~70-80. Trust plateaus at ~70. Roof and grain create real pressure.

---

### Set C: Strong Tuning (Hard Challenge)

**Trust System:**
- Change: +2 → -1 when deaths < 5 (passive trust decay)
- Change: Trust loss threshold 5 deaths → 3 deaths

**Village Health:**
- Change: malaria_harm = floor(malaria / 15)
- Change: plague_harm = floor(rat_disease / 12)
- Change: exposure_harm = floor((50 - roof) / 10) + 1
- Change: starvation_harm = floor((50 - grain) / 8) + 1

**Roof Damage:**
- Change: caterpillar_damage = floor(caterpillars / 10)
- Add: base roof decay = -2 per turn

**Grain:**
- Change: grain_loss = 3 + floor(rats / 18)
- Change: starvation threshold 40 → 50

**Eco Damage:**
- Change: wasps -= floor(ddt / 8) (was 10)
- Change: caterpillar growth = +2 always, +4 when wasps < 35

**Impact:** Passive play Village Health drops to ~50-60. Trust declines. Real pressure to intervene. DDT-heavy play is devastating.

---

## Recommendation: Set B (Moderate Tuning)

### Why Set B is the Best Next Step:

1. **Addresses Core Problem:** The fundamental issue is that passive play is too safe. Set B makes inaction costly.

2. **Balanced Challenge:** Set B creates real pressure without making the game frustrating. Players need to think but can still succeed.

3. **Target Outcomes with Set B:**
   - Passive play: Village Health ~70-80, Trust ~65-70, Score ~60-70
   - Balanced play: Village Health ~85-95, Trust ~80-90, Score ~75-85
   - DDT-heavy: Village Health ~50-65, Trust varies, Score ~45-60

4. **Minimum Changes, Maximum Impact:** Set B uses fewer changes than Set C but achieves meaningful difference.

5. **Preserves Game Feel:** The game remains playable and learnable. Set C might frustrate new players.

### Key Changes in Set B:
1. **Trust no longer rises passively** (critical fix)
2. **Village Health harm increased 30-40%** (meaningful pressure)
3. **Roof damage doubled** (ecological chain now threatening)
4. **Grain loss increased 25%** (creates resource pressure)
5. **Base roof decay added** (prevents zero-action stalemates)

### Expected Result:
After Set B tuning, players who do nothing will see Village Health decline meaningfully. The game will require active management without being punishing. Strategic DDT use will create visible ecological consequences within 6-8 turns rather than 10-12.
