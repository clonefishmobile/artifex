# 08 — Roguelike Layer

## 1. Концепция

Каждый run должен ощущаться **разным** благодаря рандомизированным choice points:
1. Carry-over choice (после каждого биома) — strategic decision что взять с собой
2. Event nodes (между биомами) — random encounters с trade-offs
3. D25+ Run Modifiers — per-run global modifiers

Цель: каждый run = unique combination, replayability + tension.

## 2. Carry-Over System (после каждого биома)

### 2.1 Trigger
После kill Boss в Wave 10 — carry-over screen появляется автоматически.

### 2.2 UI Flow
```
[Boss killed → carry-over screen]
   ↓
Игрок видит свой backpack (9 артефактов в default 3×3)
   ↓
Tap по артефакту → toggle "carry" mark (golden border)
Max 3 carry by default (Hero Board unlocks +1, +2, +3 = up to 6)
   ↓
"Watch Ad +1 carry slot" button (RV, 1× per day)
"Confirm" button → next biome
Auto-confirm после 15 sec idle
   ↓
Non-carried артефакты конвертируются в Soul Points (10 за артефакт)
```

### 2.3 Carry slots progression

| Source | Slots |
|---|---|
| Default | 3 |
| RV Ad | +1 (1× per day) |
| Hero Board D7 unlock | +1 (permanent) |
| Hero Board D14 unlock | +2 (permanent, total 5) |
| Hero Board D25 unlock | +3 (permanent, total 6) |
| Cosmic Shards 750 CS | +1 base (post-Prestige) |

### 2.4 Strategic decisions

Player должен выбирать между:
- Высокий tier артефакт (мощный сейчас)
- Cross-school combo seeds (2× different schools = optionality)
- Tier-1 building blocks (легко merge'нутся в новом биоме под new shape)

## 3. Event Nodes (между биомами)

### 3.1 Trigger
После carry-over screen — event node spawn'ится перед next biome.

### 3.2 3 Event Types

#### A. Treasure Vault (50% spawn rate)
- **Effect**: Гарантированный tier-3 артефакт (random school) + 50 gold
- **Visual**: Golden chest in dungeon corridor, hero opens it
- **Animation**: 2 sec chest opening, артефакт floats out
- **Player choice**: tap chest → take, no risk

#### B. Cursed Shrine (30% spawn rate)
- **Effect**: Tier-4 артефакт + random debuff на следующий биом
- **Possible debuffs** (random 1):
  - "Frozen Pact" — blue synergies -50% effectiveness next biome
  - "Burnt Bargain" — red synergies -50%
  - "Wilted Curse" — green synergies -50%
  - "Heavy Burden" — hero movement speed -25% next biome
  - "Brittle Bones" — hero max HP -20% next biome
- **Visual**: Dark shrine with ominous glow
- **Animation**: 3 sec ritual, артефакт + debuff icon appears
- **Player choice**: tap shrine → accept curse for tier-4

#### C. Vendor (20% spawn rate)
- **Effect**: 1 transmutation (3 одинаковых tier-3+ → random tier-4)
- **Visual**: Hooded merchant in dungeon corner
- **Animation**: Merchant accepts artifacts, returns golden tier-4
- **Player choice**: select 3 same artifacts → tap "Transmute" → random tier-4 (60% cross-school weighted)
- **Requirement**: must have 3 same tier-3+ артефакта в backpack

### 3.3 Event UI

3 cards face-down → reveal animation (1 sec) → 3 cards visible (Treasure 50%, Cursed 30%, Vendor 20%) → player taps один → effect applied → continue к next biome.

Если no choice в 30 sec → auto-pick Treasure (safe default).

## 4. D25+ Run Modifiers (Endgame Layer)

### 4.1 Trigger
После biome 4 cleared (D25+ progression unlock) — every 10 waves player получает Modifier Choice.

### 4.2 Modifier UI

Pause game → fullscreen choice modal → 3 modifier cards revealed → tap one → applies for rest of run.

### 4.3 Modifier Pool (random 3 of 8)

| Modifier | Effect | Trade-off |
|---|---|---|
| **Greedy** | +50% artifact drop rate | -25% hero max HP |
| **Berserker** | +1 active slot temporary | enemies +30% damage |
| **Acceleration** | Synergy procs +50% faster | dodge cooldown -2 sec (faster but less forgiveness) |
| **Glass Cannon** | +40% hero damage | -50% hero max HP |
| **Survivor** | +30% HP regen | -25% hero damage |
| **Lucky** | +20% crit chance, +10% rare drop | Random debuff each wave (small) |
| **Treasure Hunter** | +1 carry slot | enemies +1 spawn per wave |
| **Phoenix** | 1 free revive on death | next wave enemies +50% HP |

### 4.4 Stacking rules

Modifiers stack additively (если effects compatible). Conflicting effects (e.g. two damage modifiers) — strongest wins. Max 3 modifiers active simultaneously (oldest expires when 4th selected).

## 5. Per-biome Procedural Generation

### 5.1 Wave generation
- Wave count: 10 fixed per biome
- Enemy types: weighted by biome (Ice Castle = 70% Frost Goblin, 20% Ice Spitter, 10% Frozen Wraith — base waves; Elites + Boss fixed in waves 4, 7, 10)
- Spawn locations: random within corridor bounds (2-3 valid spawn tiles per wave)
- Enemy count: scaling per wave table (см. 02_BIOME_01)

### 5.2 Artifact drops
- Drop rate: 40-50% per kill
- School distribution: biome-specific (Ice Castle 60% blue, 25% red, 15% green)
- Tier distribution: biome-specific (Ice Castle 60% t1, 30% t2, 10% t3 — base; Elite t-3 guaranteed; Boss t-4 guaranteed)

### 5.3 Room layouts
- 3-5 layout templates per biome (random rotation)
- Layout = corridor shape + obstacle positions + spawn points
- Visual variation через biome-specific decoration (ice / fire / forest / storm / cosmic)

## 6. Daily Variance — Weekly Challenge Seed

[Already в Producer doc, но также roguelike-related:]
- Monday 00:00 UTC — global seed fixed for next 7 days
- Все игроки experience same seed (artifact drops, enemy spawns, event types)
- Top-100 leaderboard rewards cosmetics
- No social anchor required (solo competitive)

## 7. Curse / Buff Persistence

### Per-run curses (from Cursed Shrine)
- Apply only to next biome
- Show as red icon top-right во время combat
- Clear on next event node

### Per-run buffs (from D25+ Modifiers)
- Apply for rest of current run
- Show as gold icon top-right
- Clear on run end (back to Hub)

## 8. Open questions

1. Cursed Shrine — guaranteed tier-4 OR small chance for tier-3 also?
2. Vendor — may-be requirement of 3 same is too strict? Allow 3 same school (any tier)?
3. D25+ Modifier — applied to current wave or starts next wave?
4. Modifier stacking — max 3 OK, или 4-5 for endgame depth?
5. Carry-over auto-pick logic — что выбирает auto-confirm? Top-tier? Highest synergy contribution?
6. Daily variance — apply to event nodes too, или только enemy spawns?