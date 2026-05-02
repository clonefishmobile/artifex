# 03 — Backpack System

## 1. Grid Mechanics

**Grid Structure:**
Backpack представляет собой двумерную сетку ячеек (cells) размером N×M, форма которой варьируется в зависимости от биома. Каждая ячейка имеет координаты (x, y), где x ∈ [0, width-1], y ∈ [0, height-1].

**Cell States:**
- **Empty**: ячейка доступна для размещения артефакта
- **Filled**: ячейка содержит артефакт (1×1 при MVP)
- **Locked**: ячейка заблокирована и недоступна (события типа cursed shrine или постоянные препятствия)

**Placement Logic:**
- Артефакты всегда занимают 1×1 в MVP
- Drag-and-drop механика: игрок может перетащить артефакт из очереди или переместить существующий внутри grid
- Инстант-проверка на валидность: если целевая ячейка заполнена или заблокирована, placement отклоняется
- Debounce 100ms на touch slip prevention

---

## 2. Backpack Shapes Per Biome

| Биом | Shape | Ячеек | Unlock | Диаграмма |
|------|-------|-------|--------|-----------|
| Biome 1: Ice Castle | 3×3 | 9 | Старт (День 1) | [■ ■ ■] × 3 |
| Biome 2: Fire Desert | 3×4 wide | 12 | День 3 | [■ ■ ■ ■] × 3 |
| Biome 3: Ancient Forest | T-shape | 13 | День 7 | [■ ■ ■] + [■ ■ ■] + [■ ■ ■] + [■ ■ ■] |
| Biome 4: Storm Peak | 4×4 – 2 corners | 14 | День 14 | [□ ■ ■ ■] × 4, corners заблокированы |
| Biome 5: Cosmic Vault | 5×5 | 25 | День 14+ (prestige unlock D14+) | [■ ■ ■ ■ ■] × 5 |

**ASCII Диаграммы:**

```
Biome 1 (Ice Castle) 3×3:
[■][■][■]
[■][■][■]
[■][■][■]

Biome 2 (Fire Desert) 3×4:
[■][■][■][■]
[■][■][■][■]
[■][■][■][■]

Biome 3 (Ancient Forest) T-shape (13 cells):
   [■][■][■]
   [■][■][■]
[■][■][■][■][■]
   [■][■][■]
   [■][■][■]

Biome 4 (Storm Peak) 4×4 – 2 corners:
[□][■][■][■]
[■][■][■][■]
[■][■][■][■]
[■][■][■][□]

Biome 5 (Cosmic Vault) 5×5:
[■][■][■][■][■]
[■][■][■][■][■]
[■][■][■][■][■]
[■][■][■][■][■]
[■][■][■][■][■]
```

---

## 3. Artifact Properties

**Базовые свойства артефакта:**

- **School**: red (damage), blue (defense), green (sustain)
- **Tier**: 1, 2, 3, 4 (tier 4 — максимум в MVP)
- **Size**: 1×1 (стандарт для MVP)
- **Visual Representation**:
  - Иконка (icon) соответствующей школе (символ ⚔ для red, ■ для blue, ✿ для green)
  - Цветной border в зависимости от school
  - Tier indicator: количество звёздочек или цифра внутри (1–4)
- **Hover/Long-press Behavior**:
  - Отображает синергию (synergy contribution): тип школы + текущий stack count
  - Tooltip текст: "Red Tier 2 | Pyromancer Combo (3/3 red collected)"
  - Duration: 0.3s появления, остаётся до убирания пальца/мыши

**Идентификация:**
Каждый артефакт уникален в runtime (instance ID), но логически идентифицируется кортежем (school, tier).

---

## 4. Merge Rules

**Merge Condition:**
- **2 одинаковых** артефакта (same type + tier) находятся в состоянии adjacency (8-direction: горизонталь, вертикаль, диагональ)
- Merge детектируется автоматически после каждого placement

**Merge Process:**
1. Система сканирует backpack после placement нового артефакта
2. Если найдены 2 одинаковых рядом:
   - **Animation** (0.6s): 2 артефакта летят к свободной соседней ячейке → flash эффект → исчезают
   - **Result**: 1 артефакт tier+1 генерируется в целевой ячейке
   - **Audio**: ascending chime (5 разных pitches для T1→T2, T2→T3, T3→T4, T4→T5, T5)
   - **EXP начисление**: каждый merge даёт EXP (см. section 12)
3. Если merge result не помещается (целевая ячейка занята или все соседи заняты):
   - Merge не происходит
   - Pop-up уведомление: "需要свободную ячейку для merge"

**Tier Progression:**
- 2×T1 → 1×T2
- 2×T2 → 1×T3
- 2×T3 → 1×T4
- 2×T4 → 1×T5
- **Tier max: 5**

**Adjacency & Placement:**
- 8-direction (touching): 2 артефакта должны касаться друг друга (horizontal, vertical, diagonal)
- Для merge требуется минимум 1 свободная cell в окрестности результата (8 соседних cells)
- Если все 8 cells заняты → merge не происходит, pop-up tooltip

**Edge Case — Tier 5 Maximum:**
- 2× tier-5 артефакта в backpack — merge невозможен (max tier reached)
- Tap shows "Max tier" tooltip
- Артефакты остаются как есть, могут быть в active slots

---

## 5. Active Slot System

**Структура:**
- 3 active slots в нижней части экрана (Slot 1, Slot 2, Slot 3)
- Размер иконки: 100px × 100px, gap 20px между слотами (320px total width)

**Selection Mechanism:**
1. Tap артефакт в backpack → toggle в active slot
2. Visual: золотая рамка (золотая border)
3. Max 3 active. Tap 4-го → replaces oldest selection
4. Auto-locks когда лобби-таймер истекает

**Active Abilities:**
Каждый артефакт **имеет свою уникальную активную способность** (active ability). Нет fixed burst types — ability определяется конкретным артефактом.

- **Active ability** = персональный скилл артефакта (cooldown, effect, animation)
- Полный каталог артефактов с abilities — см. `11_ARTIFACTS_CATALOG.md`

**Tier Scaling:**

| Tier | Effect | Cooldown |
|------|--------|----------|
| T1 | weak version (~50% effect) | +20% cooldown |
| T2 | 70% effect | baseline |
| T3 | 100% standard | baseline |
| T4 | 130% effect | -10% cooldown |
| T5 | 200% effect | -25% cooldown |

**Synergy Contribution:**
- Артефакт в active slot **продолжает контрибутить** в passive synergies
- Stack count не теряется (артефакт считается в backpack для синергий)

**Visual Feedback:**
- Animation 0.2s при selection (scale 1.0 → 1.15 → 1.0)
- Статус под каждым слотом "Selected: X/3"

---

## 6. Synergy Contribution & Stack Counting

**Synergy Definition:**
Каждый артефакт в backpack (включая active slots) контрибутит к stack count школы.

**Stack Count Method:**
- **Count-based** (default MVP): total количество артефактов школы = stack count, независимо от tier
- **Stack count считается по штукам** (1 артефакт = 1 stack regardless of tier)
- Пример: 2× red T1 + 1× red T3 = 3 red stack count → triggers Pyromancer Combo (3-stack red synergy)
- Этот approach prevents over-stacking single high-tier артефакта
- Tier-weighted bonuses срабатывают отдельно через synergy thresholds

**Synergy Thresholds:**

| School Stack Count | Threshold Name | Bonus |
|-------------------|---|---|
| 1 | Novice | +5% school damage/defence |
| 2 | Adept | +10% + special icon tooltip |
| 3 | Pyromancer/Sentinel/Naturalist | +20% + visual effect on active slots |
| 4+ | Master | +30% + guaranteed tier+1 on next drop |

**Tier-Weighted Bonus (secondary):**
- Tier 2 artifact = 1.2x weight in synergy calc
- Tier 3 artifact = 1.5x weight
- Tier 4 artifact = 2.0x weight
- Weighted count = Σ(tier_weight × count)
- Если weighted count >5 → unlock special synergy effect (e.g., "Inferno: every 3rd hit deals 2x damage")

**Real-time Update:**
- Synergy counter обновляется instantly при placement, merge, или removal
- Visual на экране: вертикальная колонка справа с icons 3 школ + текущий count (текст + bar progress)
- Color: red #E74C3C, blue #3498DB, green #27AE60

---

## 7. Carry-Over Between Biomes

**Mechanism:**
После убийства boss биома → экран "Select Carry-Over"
- Игрок выбирает до N артефактов для carry в следующий биом (N = base 3 + expansion bonuses)
- Невыбранные артефакты → конвертируются в Soul Points (10 SP за артефакт)

**Carry Slots Base & Expansion:**

| Unlock | Carry Max | Source |
|--------|-----------|--------|
| День 1 (default) | 3 | Base |
| День 2 (RV +1) | 4 | Daily RV |
| День 7 (Hero Board) | 5 | Permanent unlock (50 Cosmic Shards) |
| День 14 (Hero Board) | 6 | Permanent unlock (100 Cosmic Shards) |
| Prestige D25 | 7 | Hero Board prestige (150 Cosmic Shards) |

**Soul Points Conversion:**
- Количество: 10 SP за каждый non-carried артефакт
- Использование: покупка boosts, key items или cosmetics в shop
- Пример: убито 8 артефактов, carried 3 → получено 50 SP

---

## 8. Backpack Expansion (Post-Prestige)

**Базовый Размер:**
- Стартовый backpack = 3×3 (9 cells)
- Первые 3 дня — фиксированная форма

**Cosmic Shards Upgrades (Permanent):**

| Стоимость (Cosmic Shards) | Upgrade | Effect |
|---|---|---|
| 100 | Grid +1 Cell | Начальный backpack 3×4 = 12 cells |
| 500 | School Choice | At prestige: выбрать starting school biome (red/blue/green) |
| 1000 | Carry +1 | +1 base carry slot (до 4 вместо 3) |
| 1500 | Grid +2 Cells | Начальный backpack 3×4 + 1 = 13 cells |
| 2000 | Merge Speed | Merge animation 0.4s вместо 0.6s |

**Progression:**
- Unlock открывается один раз,効果 permanent для всех последующих runs
- Отслеживание: Hero Board UI, вкладка "Backpack Upgrades"

---

## 9. Backpack Saturation Prevention (D30+)

**Проблема:**
При unlimited артефактах backpack превращается в decision paralysis и lag на слабых устройствах.

**Mitigation Strategies:**

### Hard Cap
- **Maximum 16 артефактов** в backpack одновременно (включая active slots)
- При достижении cap: новые артефакты попадают в **queue** (очередь ожидания)
- Queue отображается сверху экрана: горизонтальная полоса, max 6 visible, scroll right для других

### Auto-Sort Toggle
- Default ON: артефакты в grid сортируются по synergy tier (школа primary, затем tier descending)
- Toggle в UI верхний левый: иконка sort_icon
- При toggle ON → grid переставляется визуально за 0.3s

### Favorite System (D7+)
- Игрок может маркировать до 5 preferred synergies (gold star ⭐ на icon)
- UI: long-press на школу в synergy indicator → add to favorites
- Auto-sort prioritizes favorites (favorites идут первыми в grid)
- Пример: marked red + blue → в grid red слева, blue справа, green в конце

### Auto-Merge for Tier 3+ Duplicates
- Если в backpack 3+ одинаковых tier-3+ артефакта → auto-merge с pop-up confirm
- Pop-up текст: "Found 3 Tier 3 Red artifacts. Merge to Tier 4?"
- Buttons: "Confirm", "Cancel" (cancel оставляет как есть)
- Если player не подтверждает за 10 сек → auto-confirm и merge

### Smart 3-Pick UI (D14+)
- Рекомендатор активных слотов перед боем
- Анализирует current backpack + enemy composition
- Выдаёт топ 3 рекомендуемых артефакта с причиной ("Red Tier 3: 80% synergy match vs Fire Enemies")
- UI кнопка: "Auto-Select Best" → заполняет 3 active slots одним tap

---

## 10. Edge Cases & Resolution

| Ситуация | Behavior | Notes |
|----------|----------|-------|
| Backpack полон, артефакт падает | Артефакт идёт в queue | Проверка cap перед placement |
| Active slot артефакт merged | Slot остаётся активным, новый tier+1 in same slot | Priority: merge result → slot |
| Игрок drag начал но не закончил | Артефакт возвращается в исходную позицию | Touch cancel с 50ms debounce |
| Accidental drag (touch slip) | 100ms debounce перед placement | Если <100ms — игнорируется |
| Long-press на артефакт | Preview synergy (не drag) | Duration 0.5s, appears tooltip |
| Merge triggered но целевая занята | Merge отменяется, 3-й артефакт в queue | Log warning в console (debug only) |
| Active slot removed (merge/carry) | Slot остаётся пуст | Следующий placement заполняет |
| Grid полная, queue полная (>10) | Новые drops отклоняются | Screen notification: "Backpack full, clear some items" |
| Double-tap на артефакт | Toggle select (add/remove active slot) | 300ms window для double-tap |
| Drag между биомами | Carryover отменяет всё в process | Только selected carry проходят |
| 2 одинаковых tier-5 в backpack | Merge невозможен (max tier reached) | Tap shows 'Max tier' tooltip. Артефакты остаются как есть, могут быть в active slots |
| Auto-merge для duplicates выше T3 | Confirm pop-up или silent? | See open question section 14 |

---

## 11. UI Specifications

**Grid Layout:**

| Component | Size | Position | Notes |
|-----------|------|----------|-------|
| Grid container | 3×3 = 240×240px (Ice Castle) | Center screen | Padding 20px от edges |
| Cell size | 80×80px | Grid | 2px gap между cells |
| Artifact icon | 64×64px | Center cell | Border 3px, color per school |
| Tier indicator | 16px text | Bottom-right corner | Arial Bold, color white #FFF |

**Queue (Artifact Waiting List):**

| Component | Size | Position | Notes |
|-----------|------|----------|-------|
| Queue row | 6 visible × 80px wide = 480px | Top screen | Horizontal scroll, 8px padding |
| Queue label | "Queue (3/10)" | Left of row | Subtle text #AAA |
| Queue cell | 80×80px | Row | Same style as grid |

**Active Slots:**

| Component | Size | Position | Notes |
|-----------|------|----------|-------|
| Slot container | 3 × 100px + gaps | Bottom center | Gap 20px, total ~320px width |
| Slot icon | 100×100px | Container | Border 5px (gold if active) |
| Slot label | "Slot 1" | Below icon | Text 12px, grey #777 |
| Counter | "Selected: X/3" | Below slot | Dynamic, updates on toggle |

**Synergy Indicator (Right Side):**

| Component | Size | Position | Notes |
|-----------|------|----------|-------|
| Column | 60px wide | Right screen | Vertical stack, 10px gap |
| School icon | 32×32px | Column | Icon per school (⚔ ■ ✿) |
| Count text | "3/5" | Below icon | Bold 14px font |
| Progress bar | 40px wide × 8px tall | Below text | Filled % based on next threshold |
| Color | Red/Blue/Green | Icon & bar | Match school |

---

## 12. Backpack Shapes Detail Reference

### Biome 1 — Ice Castle (3×3, 9 cells)
```
Coordinates:
(0,0) (1,0) (2,0)
(0,1) (1,1) (2,1)
(0,2) (1,2) (2,2)

All cells: empty (no locked)
```

### Biome 2 — Fire Desert (3×4, 12 cells)
```
Coordinates:
(0,0) (1,0) (2,0) (3,0)
(0,1) (1,1) (2,1) (3,1)
(0,2) (1,2) (2,2) (3,2)

All cells: empty
```

### Biome 3 — Ancient Forest (T-shape, 13 cells)
```
Top section (rows 0-1, cols 1-3):
      (1,0) (2,0) (3,0)
      (1,1) (2,1) (3,1)

Middle section (row 2, cols 0-4):
(0,2) (1,2) (2,2) (3,2) (4,2)

Bottom section (rows 3-4, cols 1-3):
      (1,3) (2,3) (3,3)
      (1,4) (2,4) (3,4)

Total: 3 + 3 + 5 + 3 + 3 = 13 cells
```

### Biome 4 — Storm Peak (4×4 – 2 corners, 14 cells)
```
Coordinates:
(0,0) LOCKED   (1,0) (2,0) (3,0)
(0,1) (1,1)    (2,1) (3,1)
(0,2) (1,2)    (2,2) (3,2)
(0,3) (1,3)    (2,3) (3,3) LOCKED

14 playable cells (16 - 2 corners)
```

### Biome 5 — Cosmic Vault (5×5, 25 cells)
```
Full 5×5 grid, no locked cells:
(0,0)...(4,0)
(0,1)...(4,1)
(0,2)...(4,2)
(0,3)...(4,3)
(0,4)...(4,4)

All 25 cells available
Unlock: Prestige Day 14+
```

---

## 12. EXP / Hero Level System

Каждый успешный merge на **свободную cell** начисляет **EXP** в hero level progression bar.

### 12.1 EXP per merge

| Merge | EXP награда |
|---|---|
| 2× T1 → 1× T2 | +5 EXP |
| 2× T2 → 1× T3 | +15 EXP |
| 2× T3 → 1× T4 | +45 EXP |
| 2× T4 → 1× T5 | +135 EXP |

EXP scaling — exponential (×3 на каждом tier). Это поощряет high-tier merges как milestone events.

### 12.2 EXP bar UI

- Тонкая горизонтальная полоска под top status bar (~5 px height)
- Цвет: gradient gold → bright gold (заполнение)
- Текст: "Lv X — Y/Z EXP" в правой части bar
- Visible во всех phases (lobby + combat + Hub)

### 12.3 Merge animation + EXP

- 2 артефакта летят к target cell → 0.4s
- Flash effect → 0.1s
- Tier+1 артефакт появляется → 0.1s
- **Particle burst** — золотые EXP particles летят от merge point в EXP bar (0.8s travel)
- EXP bar fills smoothly (lerp animation)

### 12.4 Hero Level Up

- Bar заполнен полностью → triggers Level Up
- Bonuses per level:
  - **+5% hero max HP** (permanent для current run)
  - **+2% hero damage** (permanent для current run)
  - **+1 random small bonus**: random selection из pool (например +1% crit chance, +1% dodge cooldown reduction, +5 base damage, и т.д.)
- Visual: golden flash на hero, "LEVEL UP!" text overlay для 1.5s
- Audio: triumphant chime
- Combat не паузится (level up applies immediately если в combat)

### 12.5 EXP scaling по уровням

- Level 1 → 2: requires 100 EXP
- Level X → X+1: requires `100 × 1.20^X` EXP
- Level 5: ~250 EXP needed
- Level 10: ~620 EXP
- Level 20: ~3833 EXP
- Realistic max в одном run: Level 12-18 (через 50-100 merges)

### 12.6 EXP reset & conversion

- На end of run — hero level resets к 1
- Накопленный progress конвертируется в Soul Points (50 Soul per level reached)
- Это создаёт meta progression incentive: высокий level = больше Soul Points = faster Prestige

### 12.7 Open questions для EXP system

1. Random level-up bonus pool — сколько вариантов? 5 / 10 / 20?
2. Should level cap exist? (Currently no cap.)
3. EXP boost через RV (watch ad для 2× EXP next merge)?
4. Permanent EXP boost через Cosmic Shards?

---

## 13. Technical Notes for Implementation

**Data Structure:**
```
class Artifact {
  id: UUID
  school: "red" | "blue" | "green"
  tier: 1 | 2 | 3 | 4
  position: {x, y} | null (если в queue)
  isActive: boolean
  isLocked: boolean (если selected для carry)
}

class Backpack {
  grid: Cell[][]
  maxCells: number
  queue: Artifact[]
  activeSlotsCount: number
}

class Cell {
  x: number
  y: number
  artifact: Artifact | null
  isLocked: boolean (grid-level lock)
}
```

**Merge Detection Algorithm:**
1. After placement, iterate всех cells
2. Для каждого cell с артефактом: check 8 соседей
3. Collect all neighbors same (school, tier)
4. Если count >= 3: trigger merge (take first 3 matched)

**Performance Optimization:**
- Grid update: O(n) per placement (n = cells)
- Merge detection: O(n) post-placement
- Queue management: FIFO, max 10 visible
- Render optimization: virtualize queue за пределами viewport

---

## 14. Open Questions for Stakeholders

1. **EXP per merge — exponential scaling правильный (×3) или линейный лучше?**
   - Текущее: exponential (×3 per tier) — encourages high-tier merges
   - Альтернатива: linear (+10 per merge) — more steady progression
   - Decision impact: milestone feel vs gradual progression

2. **Hero level up bonus — auto-applied или player choice (3 options)?**
   - Текущее: random auto-applied
   - Альтернатива: player selects 1 из 3 options (like Slay the Spire)
   - Decision impact: agency vs simplicity

3. **Tier-weighted Stack vs Count-weighted?**
   - Текущее предложение: count-weighted (3 tier-1 = 3 tier-3)
   - Альтернатива: tier-3 = 2× multiplier (3 tier-1 ≈ 1.5 tier-3)
   - Decision impact: balance synergy thresholds

4. **Auto-merge for Tier 3+ — Silent vs Confirm Pop-up?**
   - Текущее: pop-up с buttons
   - Альтернатива: silent merge с toast notification (no block)
   - Decision impact: UX friction vs clarity

5. **Active Slot Change During Combat?**
   - Текущее: locked после лобби countdown
   - Альтернатива: changeable during combat (but limited, e.g., 1× per run)
   - Decision impact: strategy depth vs complexity

6. **Locked Cells via Events?**
   - Cursed Shrine event может заблокировать 1-2 cells временно (D7+)?
   - Временность: до next biome или до unlock event?
   - Impact: adds puzzle element

7. **Special Artifacts (Legendary Tier 5)?**
   - Cross-school артефакты (e.g., red+blue hybrid)?
   - Size: 1×1 или 2×2?
   - Merge rules: как комбинируются?
   - Decision: complexity vs rarity value

8. **Queue Auto-cleanup**
   - Если в queue артефакт >5 turns и backpack имеет space → auto-move?
   - Или player must manually drag из queue?
   - Decision impact: QoL vs player agency
