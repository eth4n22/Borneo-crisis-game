# Full Balance & Logic Audit - Borneo Crisis

## PHASE 1 — ACTION / CARD VALUES AUDIT

### Current Card Effects (from source code):

| Card | Cost | Immediate Effects | Delayed Effects | Assessment |
|------|------|-------------------|-----------------|-------------|
| **Spray DDT** | 12 | Mosq -18, DDT +20, Trust +2 | Ecology queue → cats -X, rats +X | Too strong on disease control, ecological backlash too delayed |
| **Cat Drop** | 15 | Trust +1 | Cats +18 (or +9), Rats -10 | Cost-effective but delayed, good balance |
| **Awareness** | 10 | Trust +3, Disease -3 each | +4 disease reduction delayed | Cost-effective, trust builder - TOO STRONG |
| **Repair Thatch** | 8 | Roof +20 (max 80) | None | Now properly capped, reasonable |
| **Protect Grain** | 6 | Grain +12 (max 85) | None | Now properly capped, reasonable |
| **Medical Aid** | 5 | Disease -8 each | None | Very cheap, very strong - TOO STRONG |
| **Bonus Funding** | 0 | Budget +15 | None | Free money, feels good when drawn |

### Issues Identified:
1. **Awareness Campaign** - Cost 10 gives +8 trust total (3 now + 5 later). This is extremely efficient for trust building.
2. **Medical Aid** - Cost 5 gives -16 disease total (8 now + 8 later). This is too cheap for the value.
3. **Repair Thatch** - Now capped at 80 (was 100). This is appropriate.
4. **Protect Grain** - Now capped at 85 (was 100). This is appropriate.

---

## PHASE 2 — DAMAGE / RECOVERY SYSTEMS AUDIT

### Current Harm Formulas (from computeDeaths):
```
malaria_harm = floor(malaria_infected / 18)
plague_harm = floor(rat_disease_infected / 15)
starvation_harm = floor((50 - grain_stores) / 10) + 1 (if grain < 50)
exposure_harm = floor((50 - roof_integrity) / 10) + 1 (if roof < 50)
```

### Current Natural Dynamics (from applyNaturalDynamics):
```
mosquitoes: += round((60 - mosquitoes) * 0.10)  // Natural recovery toward 60
ddt: -= 8  // DDT decays
cats: += 1 - floor(rats / 25)  // Cats recover slowly even with rats high
rats: += 4 + floor((50 - cats) / 20)  // Rats grow unless cats very high
malaria_infected: += floor((mosquitoes - malaria_infected) * 0.25)
rat_disease_infected: += floor((rats - rat_disease_infected) * 0.20)
grain_loss: = 2 + floor(rats / 18)  // Updated to stronger
roof_damage: = floor(caterpillars / 12)
```

### Trust System:
```
Base decay: -1 per turn
Deaths >= 5: -floor(deaths_this_turn / 3)
```

### Issues Identified:
1. **Grain loss** - Now `2 + floor(rats / 18)` is reasonable
2. **Roof damage** - `floor(caterpillars / 12)` is reasonable but requires caterpillars to build up
3. **Natural malaria recovery** - Mosquitoes naturally decrease toward 60, malaria follows - makes passive play safe
4. **Starvation/Exposure thresholds** - Now at 50 instead of 40, triggers more easily

---

## PHASE 3 — SCORE FORMULA AUDIT

### Current Score Formula:
```
deaths_penalty = min(60, deaths_cumulative * 0.6)
peak_penalty = min(20, peak_infection * 0.1)
duration_penalty = min(15, outbreak_duration * 1.5)
eco_penalty = min(20, eco_events * 5)
roof_collapse_penalty = 5 if final_roof < 30
grain_collapse_penalty = 5 if final_grain < 30

trust_bonus = min(10, floor(final_trust / 10))
budget_bonus = min(5, floor(final_budget / 20))

score = 100 - penalties + bonuses
```

### Score Factor Rankings (by max potential impact):
1. **Deaths** - Max 60 pts (60% of total penalty potential)
2. **Eco Collapse** - Max 20 pts
3. **Peak Infection** - Max 20 pts
4. **Outbreak Duration** - Max 15 pts
5. **Roof/Grain Collapse** - Max 10 pts combined
6. **Trust Bonus** - Max +10 pts (nearly automatic)
7. **Budget Bonus** - Max +5 pts (easy to achieve)

### Issues:
- Deaths dominate the score (intended)
- Trust bonus is nearly automatic since trust decays only -1/turn and actions give trust bonuses
- Budget bonus is easy since passive income is +3/turn

---

## PHASE 4 — TURN SUMMARY LOGIC AUDIT

### Current logEvent function (lines 3830-3913):
The summary shows:
- Player name, turn, action
- Narrative description
- Budget change
- Current stats (mosquitoes, cats, rats, malaria, rat disease, trust)
- Delayed effect narratives (if any)
- Deaths this turn

### Problems Identified:
1. **Shows current values, not changes** - Doesn't show what changed between turns
2. **No roof/grain display** - Critical resources not shown in summary
3. **No comparison to previous** - Can't see if Repair helped if already at max
4. **Summary says "Roof integrity worsened"** - This happens when roof is capped at max, can't show improvement
5. **Protect Grain not clearly reflected** - No grain change shown
6. **Missing key variables** - Doesn't show wasps, caterpillars, village health

---

## PHASE 5 — TEST SCENARIOS

### Scenario A: Passive Play (Do Nothing each turn)
- Starting: Village 100, Trust 70, Roof 80, Grain 85
- Turn 1: Trust 69, Village 100 (malaria 41, no harm), Roof 80, Grain 83
- Turn 6: Trust 64, Village 100, Roof 80, Grain 78
- Turn 12: Trust 59, Village 100, Roof 80, Grain ~70

**Issue**: Village Health NEVER drops with passive play. Game is too forgiving.

### Scenario B: Balanced Play (Awareness + Cat Drop + Repair)
- Turn 1: Awareness → Trust +8, Disease -3
- Turn 2: Cat Drop → Cats +18, Rats -10
- Turn 3: Repair → Roof 80 (already maxed)
- Turn 12: Trust 100, Village 100, Roof 80, Rats 0

**Issue**: Game becomes trivial with balanced actions.

### Scenario C: Resource-focused (Repair + Grain + Medical Aid)
- Medical Aid is too cheap for value
- Repair/Grain properly capped now
- But actions still too powerful relative to harm

### Scenario D: DDT-heavy
- DDT builds quickly → wasps die → caterpillars grow → roof damage
- This path now works but takes many turns
- Late game eco collapse is real threat

### Scenario E: Awareness-heavy
- Trust builds to 100 quickly
- Disease decreases
- Village stays at 100
- Score ~95+

---

## PHASE 6 — RANKED DIAGNOSIS

### Top Issues Remaining:

1. **TOO FORGIVING**: Village Health harm formulas too weak
   - Max harm = ~5-6 per turn even with high disease
   - Passive play maintains 100 health easily

2. **AWARENESS TOO STRONG**: Cost 10 gives +8 trust total
   - Makes trust too easy to maintain
   - Trust bonus nearly automatic

3. **MEDICAL AID TOO STRONG**: Cost 5 gives -16 disease total
   - Too cheap for the value
   - Makes disease management trivial

4. **TURN SUMMARY MISSING INFORMATION**:
   - Doesn't show changes, only current values
   - No roof/grain displayed
   - Misleading when roof already at max

5. **NATURAL RECOVERY TOO STRONG**:
   - Mosquitoes naturally decrease
   - Cats naturally recover
   - Makes passive play too safe

6. **ROOF/GRAIN CARS CAPPED BUT**:
   - Summary doesn't reflect the cap properly
   - "Roof integrity worsened" still appears when at max

---

## PHASE 7 — RECOMMENDED FIX SETS

### Set A: Mild Adjustments
- Medical Aid: Cost 5 → 8 (increase)
- Awareness: Trust +8 → +5 total (3 now + 2 delayed)
- Turn Summary: Add roof/grain display

### Set B: Moderate Adjustments (RECOMMENDED)
- Medical Aid: Cost 5 → 8
- Awareness: Trust +8 → +4 total (2 now + 2 delayed)
- Village Health harm: Increase by 20%
- Turn Summary: Show changes, add roof/grain, fix cap messages

### Set C: Strong Adjustments
- Medical Aid: Cost 5 → 10, effect -12 disease
- Awareness: Trust +8 → +2 total
- All harm formulas: Increase by 30-40%
- Turn Summary: Complete overhaul

---

## PHASE 8 — CHOSEN SET (Moderate - Set B)

### Reasoning:
Set B addresses the most critical issues without making the game frustrating. It:
1. Makes Medical Aid less trivial (cost 5→8)
2. Makes Awareness less dominant (trust +8→+4)
3. Increases Village Health pressure (~20%)
4. Fixes the turn summary to show actual changes and roof/grain

This keeps the game educational and fair while improving feedback accuracy.

---

## IMPLEMENTATION CHANGES NEEDED:

1. **Card values**: 
   - Medical Aid cost 5→8
   - Awareness trust bonus 3+5 → 2+2

2. **Harm formulas**:
   - Increase by ~20% to make Village Health pressure real

3. **Turn summary**:
   - Show before/after for key variables
   - Add roof and grain display
   - Fix messages when at max capacity

4. **Document values**:
   - Update How to Play with accurate information