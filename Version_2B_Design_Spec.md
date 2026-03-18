# Version 2B Design Specification: The Ecological Chain Expansion

## 1. VERSION 2B TEACHING GOAL

**Core Learning Objective:**
Version 2B should demonstrate that DDT creates multiple ecological pathways of unintended consequences — one intervention can destabilize insect, animal, and resource systems simultaneously through hidden cascade effects.

**Key Systems-Thinking Concepts to Teach:**
- Interventions have delayed, indirect effects across multiple trophic levels
- Ecological chains can create surprising feedback loops
- Short-term disease control may cause long-term ecological collapse
- Some effects are invisible until they suddenly manifest

---

## 2. NEW VARIABLES TO ADD

### 2.1 Wasp Population (visible)
| Attribute | Value |
|-----------|-------|
| Variable | `wasps` |
| Represents | Natural predators that keep caterpillar populations in check |
| Starting Value | 50 |
| Range | 0-100 |
| Visibility | **Visible** in main stats panel |

### 2.2 Caterpillar Population (visible)
| Attribute | Value |
|-----------|-------|
| Variable | `caterpillars` |
| Represents | Leaf-eating insects that damage thatch roofs |
| Starting Value | 20 |
| Range | 0-100 (can exceed 100) |
| Visibility | **Visible** in main stats panel |

### 2.3 Gecko Population (internal)
| Attribute | Value |
|-----------|-------|
| Variable | `geckos` |
| Represents | Small lizards that are eaten by cats |
| Starting Value | 40 |
| Range | 0-100 |
| Visibility | **Hidden** — internal ecosystem tracker |

### 2.4 Gecko Toxicity (internal)
| Attribute | Value |
|-----------|-------|
| Variable | `gecko_toxicity` |
| Represents | Accumulated DDT in gecko tissue (bioaccumulation) |
| Starting Value | 0 |
| Range | 0-100 |
| Visibility | **Hidden** — internal only |

---

## 3. VISIBILITY RECOMMENDATION

### Visible in Main UI:
- **Wasps** — visible stat, tracked in charts
- **Caterpillars** — visible stat, tracked in charts
- Add to existing stats display alongside Mosquitoes, Rats, Cats

### Hidden/Internal Only:
- **Geckos** — abstract concept, not shown to players
- **Gecko Toxicity** — invisible bioaccumulation, players only see the effect (cat deaths)

### Rationale:
Players already manage 11 visible variables. Adding 2 more (wasps, caterpillars) is manageable. Geckos should remain abstract — players will see "cats dying from toxic prey" without needing to understand the gecko intermediary.

---

## 4. FORMULAS / UPDATE LOGIC

### 4.1 Wasp Dynamics
```
// Wasps decline from DDT (direct)
wasps -= floor(ddt_level / 8)

// Natural wasp recovery when not suppressed
if (wasps < 30):
    wasps += 2
else:
    wasps += 1
```

### 4.2 Caterpillar Growth
```
// Caterpillars multiply when wasps are low (natural predator removed)
if (wasps < 25):
    caterpillars += 4 + floor((50 - wasps) / 15)
else:
    caterpillars += 1
```

### 4.3 Caterpillar Roof Damage
```
// Caterpillars eat thatch, damaging roof integrity
caterpillar_damage = floor(caterpillars / 15)
roof_integrity -= caterpillar_damage
```

### 4.4 Gecko Bioaccumulation
```
// Geckos absorb DDT from eating contaminated insects
if (ddt_level > 10):
    gecko_toxicity += floor(ddt_level / 15)
else:
    gecko_toxicity -= 1  // Slowly detoxify
```

### 4.5 Cat Harm from Toxic Geckos
```
// Cats eat toxic geckos and die
if (gecko_toxicity > 30):
    cat_deaths = floor((gecko_toxicity - 30) / 20) + 1
    cats -= cat_deaths
```

### 4.6 Integration with Existing Systems
All new variables connect to existing model:
- **Caterpillars → Roof** (new damage pathway)
- **Gecko Toxicity → Cats** (new death pathway)
- **Cats → Rats** (existing: fewer cats = more rats)
- **Rats → Grain + Plague** (existing: more rats = less grain + more disease)

---

## 5. INTEGRATION WITH CURRENT SYSTEM

### Connection Map:

```
DDT Level
    ↓
    ├─→ Wasps ↓ (direct harm)
    │       ↓
    │       └─→ Caterpillars ↑ (predator removed)
    │               ↓
    │               └─→ Roof Damage ↑ (caterpillar eating)
    │
    └─→ Gecko Toxicity ↑ (bioaccumulation)
            ↓
            └─→ Cats ↓ (toxic prey harm)
                    ↓
                    └─→ Rats ↑ (fewer predators)
                            ↓
                            ├─→ Plague ↑
                            └─→ Grain ↓
```

### Unchanged Systems:
- Village Health computation stays the same
- Trust, Budget, Scoring unchanged
- Card actions remain as-is

---

## 6. ACTION / CARD IMPLICATIONS

**Recommendation: No new cards needed for Version 2B**

The existing 7 cards already affect the new variables:
- **Spray DDT** → reduces wasps, increases gecko toxicity
- **Cat Drop** → helps restore cats (counteracts gecko toxicity harm)
- **Repair Thatch** → repairs roof damage from caterpillars

No new actions required — the ecological chain emerges naturally from existing interventions.

---

## 7. UI / DASHBOARD CHANGES

### Add to Stats Panel:
```
Current Stats Display:
- Village Health: [███░░] 75
- Roof Integrity: [████░] 80  ← ADD CATERPILLAR DAMAGE HERE
- Grain Stores:   [███░░] 65

Ecosystem Status:
- Mosquitoes: 70    ← ADD WASPS HERE
- Rats: 20         ← ADD CATERPILLARS HERE
- Cats: 60
```

### Recommended Placement:
1. **Wasps** — Add to "Ecosystem Status" section near Mosquitoes
2. **Caterpillars** — Add to same section, near Rats

### Event Log Updates:
Add narrative messages:
- "🐝 Wasp populations declining from DDT use"
- "🐛 Caterpillars swarm — thatch damaged!"
- "🦎 DDT accumulating in small prey"
- "🐱 Cats sick from eating toxic prey"

---

## 8. RESULTS / CHART CHANGES

### New Chart Options:
1. **Wasps vs Caterpillars** — Shows inverse relationship over time
2. **Roof Breakdown** — Stacked: natural decay + rat damage + caterpillar damage
3. **Cat Mortality Sources** — Existing deaths vs gecko toxicity deaths

### Minimal Implementation:
- At minimum, add Wasps and Caterpillars to existing "Ecosystem" chart
- No new chart strictly required

---

## 9. RISKS / COMPLEXITY

### High Risk Items:
1. **Caterpillar Effect Too Weak** — If roof damage from caterpillars is negligible, players won't notice the pathway
2. **Gecko Mechanic Too Hidden** — Players may not understand why cats are dying
3. **Too Many Visible Stats** — Adding 2 more visible variables may overwhelm players

### Mitigation Strategies:
- Make caterpillar roof damage meaningful (at least 2-3 points per turn when caterpillars > 30)
- Add clear event log messages explaining gecko pathway
- Keep Geckos/Gecko Toxicity hidden — only show effects, not mechanism

---

## 10. FINAL RECOMMENDATION

### Version 2B Specification:

**New Variables:**
| Variable | Type | Start | Range | Visible |
|----------|------|-------|-------|---------|
| `wasps` | Number | 50 | 0-100 | YES |
| `caterpillars` | Number | 20 | 0-∞ | YES |
| `geckos` | Number | 40 | 0-100 | NO |
| `gecko_toxicity` | Number | 0 | 0-100 | NO |

**Recommended Formulas:**
- Wasps decline from DDT: `-floor(ddt/8)`
- Caterpillars grow when wasps low: `+4 + floor((50-wasps)/15)` 
- Caterpillars damage roof: `-floor(caterpillars/15)`
- Gecko toxicity from DDT: `+floor(ddt/15)`
- Cat deaths from toxicity: `floor((toxicity-30)/20)+1` when toxicity > 30

**Visibility:**
- Wasps and Caterpillars: **VISIBLE** in main UI
- Geckos and Gecko Toxicity: **HIDDEN** (internal only)

**Actions:** No new cards required

**Implementation Priority:** 
✅ Can be implemented now — adds meaningful ecological depth without feature creep
⚠️ Monitor player feedback on complexity — if overwhelmed, hide caterpillars too

---

## Version 2B Summary

Version 2B adds the hidden ecological chain that demonstrates how DDT creates unintended consequences across multiple trophic levels. Players will discover that spraying DDT affects not just mosquitoes, but also wasps (beneficial), caterpillars (roof damage), and eventually cats (through gecko bioaccumulation). This creates a more realistic and challenging ecological simulation while teaching systems-thinking about indirect interventions.
