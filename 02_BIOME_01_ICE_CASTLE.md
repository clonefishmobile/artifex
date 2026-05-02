# 02 — Biome 1: Ice Castle

**Тематика:** Замёрзшая крепость / catacombs of the Frost King.
**Камера:** First-person dungeon crawler.
**Длительность:** 5-10 минут (10 комнат).
**Backpack shape:** 3×3 (стандартная стартовая форма).
**Школа артефактов в биоме:** преимущественно **Blue** (60% drops), Red 25%, Green 15%.

---

## Визуальная атмосфера

- Каменные стены покрытые инеем, ледяные сосульки свисают с потолка
- Холодный голубой освещение, дыхание героя видно на холодном воздухе
- Snow particles в combat phase
- Звук: ветер, эхо, скрип льда
- Палитра: cyan / white / dark grey / silver
- Tile size: 1 tile = 2 шага героя

---

## Враги биома (Ice Castle bestiary)

| Enemy | Тип | HP | Damage | Behavior | Drop chance |
|---|---|---|---|---|---|
| **Frost Goblin** | Basic melee | 100 | 8 | Auto-walks к hero, swing club | 40% (60% blue, 25% red, 15% green) |
| **Ice Spitter** | Ranged | 80 | 12 | Стоит на месте, кидает ice balls (slow projectile) | 45% (70% blue) |
| **Frozen Wraith** | Basic | 120 | 10 | Floats slowly, can phase through 1 enemy | 40% |
| **Glacial Brute** | Elite | 250 | 18 | Slow movement, ground-pound AOE telegraph | 100% (guaranteed t-3) |
| **Ice Warden** | Boss | 1000 | 25 | См. секцию Boss | Guaranteed t-4 |

HP scaling: при wave 1 = base, wave 2 = +10%, wave 3 = +20%, и т.д. (linear до wave 10).

---

## Структура 10 комнат

### Комната 1 — Promerзшие Ворота (Easy)

**Тематика:** Ледяной портал — герой только что вошёл в крепость.

**Враги:** 3× Frost Goblin

**Layout:** Узкий коридор с одной дверью в конце. Враги appears через дверь спереди по одному.

**Artifact drops:** ~1 артефакт (40% × 3 = ~1.2)

**Goal:** Tutorial encounter. Учит игрока:
- Hero auto-walks вперёд
- Hero auto-attacks ближайшего
- Tap по слоту = burst (если есть)

---

### Комната 2 — Ледяная Кладовая (Easy)

**Враги:** 4× Frost Goblin

**Layout:** Маленькая прямоугольная комната. Goblins spawn'ятся по 2 одновременно.

**Artifact drops:** ~1.6 артефакта

**Goal:** Учит multi-target awareness. Hero auto-attacks нужного, но игрок может tap'нуть для focus fire.

---

### Комната 3 — Коридор Теней (Easy)

**Враги:** 5× Frost Goblin (приходят волной)

**Layout:** Длинный коридор. Все 5 врагов идут одной волной к hero.

**Artifact drops:** ~2 артефакта

**Goal:** Hero overwhelmed mass'ой = время использовать burst (Missile Strike или Plague Swarm).

---

### Комната 4 — Зал Стражей (ELITE SPIKE)

**Враги:** 1× Glacial Brute + 2× Frost Goblin

**Layout:** Большой зал, Brute стоит посреди, Goblins по бокам.

**Special:** Brute телеграфирует ground-pound AOE (red circle на полу 2 секunды до взрыва). **Side-step dodge** required чтобы избежать.

**Artifact drops:** Brute гарантированно дропает tier-3 + ~0.8 от Goblins

**Goal:** Первая elite encounter. Учит:
- Side-step dodge (swipe LEFT/RIGHT)
- Focus fire (tap на Brute чтобы сначала убить)
- Telegraph reading (red AOE warning)

---

### Комната 5 — Тихий Перевал (REST)

**Враги:** 3× Frost Goblin

**Layout:** Маленькая комната. Easy enemies.

**Special:** **+1 guaranteed artifact drop** в конце комнаты (свободный артефакт на полу — иногда tier-2).

**Artifact drops:** ~1.2 + 1 guaranteed

**Goal:** Передышка после Brute. Время merge'нуть собранные артефакты в lobby phase.

---

### Комната 6 — Ледяной Грот (REST)

**Враги:** 4× Frost Goblin + 1× Ice Spitter (ranged)

**Layout:** Открытое пространство с пилларами (cover). Ice Spitter stands behind pillar, остальные melee.

**Special:** Первая ranged encounter. Ice balls slow projectiles — игрок должен side-step.

**Artifact drops:** ~2 артефакта

**Goal:** Учит ranged dodge mechanics.

---

### Комната 7 — Зал Двойной Угрозы (ELITE x2)

**Враги:** 2× Glacial Brute + 1× Frost Goblin

**Layout:** Большой зал, 2 Brutes по диагонали, Goblin посередине.

**Special:** 2 Brutes одновременно делают AOE telegraph — нужно double-dodge.

**Artifact drops:** 2× guaranteed tier-3 от Brutes + ~0.4

**Goal:** Pre-boss prep. Hard encounter, требует skillful использование burst slots + dodges.

---

### Комната 8 — Ледяной Лабиринт (ESCALATION)

**Враги:** 8 mixed (4× Goblin + 2× Spitter + 2× Wraith)

**Layout:** Маленькая комната, враги приходят с трёх сторон в 2 волны (по 4).

**Special:** Wraiths могут пройти через goblins (phase) — добираются до hero быстрее.

**Artifact drops:** ~3.2 артефакта

**Goal:** Стресс-test mass combat. Burst slots все на cooldown — hero должен выживать.

---

### Комната 9 — Преддверие Короля (ESCALATION)

**Враги:** 10 mixed (5× Goblin + 3× Spitter + 2× Wraith)

**Layout:** Длинный коридор с volleys врагов.

**Special:** Last room before boss. После clear — hero автоматически идёт в boss chamber. Время сделать lobby с финальным merge перед Ice Warden.

**Artifact drops:** ~4 артефакта

**Goal:** Финальный prep. Игрок должен иметь:
- Минимум 1 high-tier red артефакт (для damage)
- Минимум 1 blue (для shield)
- 3 active slots с burst готовыми

---

### Комната 10 — Тронный Зал Ледяного Стража (BOSS)

**Враг:** Ice Warden (1000 HP, 4 фазы)

**Layout:** Огромный зал с замёрзшим троном в центре. Ice Warden — гигантский ледяной голем.

#### Phase 1 (100-75% HP): "Awakening"
- Slow ranged attacks (ice spears, 1 spear каждые 3 сек)
- Hero auto-walks to melee range, удар-удар-удар
- Side-step dodge для уклонения от spears

#### Phase 2 (75-50% HP): "Ice Storm"
- Adds AOE telegraph attacks: red circle на полу 2 сек warning, потом ice explosion
- 1 AOE каждые 5 сек, на random tile рядом с hero
- Hero должен side-step из circle до взрыва

#### Phase 3 (50-25% HP): "Summoning"
- Призывает 2× Frost Goblin minions (одну волну)
- Continues slow attacks параллельно
- Игрок должен быстро убить minions (tap focus fire) и вернуться к Warden

#### Phase 4 (25-0% HP): "Berserk"
- Accelerated patterns: spears каждые 1.5 сек, AOE каждые 3 сек
- Boss движется быстрее, агрессивно подходит
- Final DPS race

#### Reward на kill
- Гарантированный tier-4 артефакт (выбор: Blue Crown или Frostforge weapon)
- 50 gold
- 1 Cosmic Shard fragment
- Triggers CARRY-OVER SCREEN

---

## Drop tables биома

| Tier | Probability | Notes |
|---|---|---|
| Tier 1 | 65% | Стандартные blue/red/green базовые артефакты (Frostbite Sigil T1, Crimson Core T1, Verdant Spore T1) |
| Tier 2 | 25% | Усиленные (через merge или редкий drop) |
| Tier 3 | 10% | Из Glacial Brute guaranteed, из обычных enemy rare |
| Tier 4 | Boss only | Ice Warden гарантированно дропает 1× T4 артефакт |
| Tier 5 | Не дропает в биоме 1 | Достигается только через merge (2× T4 → T5). Cosmic Vault биом 5 имеет rare T5 drops. |

**School distribution в Ice Castle** (биом-aligned):
- **Blue: 60%** (Frostbite Sigil, Permafrost Cube)
- Red: 25% (Crimson Core, Pyrohelm)
- Green: 15% (Verdant Spore)

Конкретные артефакты в drop pool — см. `11_ARTIFACTS_CATALOG.md` секцию 7.

---

## Биом-specific synergies (рекомендуемые стратегии)

С учётом 60% blue drops, оптимальные builds:

**Build A: "Ice Tank"**
- Stack 3-5 blue → "Glacial Ward" (-12% damage taken) → "Permafrost Pulse" (heal aura)
- Active slots: Barrier Field × 2 + 1 Time Warp (если повезёт)
- Защитный playstyle, идеален против boss Phase 4

**Build B: "Frostforge Hybrid"**
- 3 red + 2 blue в backpack → cross-school combo "Frostforge" (damage + freeze chance)
- Active slot: Berserk burst (R+G) + Missile Strike + Time Warp
- Aggressive playstyle, быстрый clear

**Build C: "Plague Spreader"**
- Mix green + red → poison spread synergy
- Менее оптимален в Ice Castle (зелёных мало), но работает если игрок carry'нул green из predыдущих биомов

---

## Биом-specific события

После Ice Warden kill — переход на event node, потом следующий биом (Fire Desert или Ancient Forest, random selection).

---

## Open questions (для биом-дизайна)

1. **Boss arena камера** — оставаться first-person или переключиться на cinematic over-shoulder в boss phase 4?
2. Snow particles density — производительность на low-end devices? Dynamic quality scaling?
3. Echo/reverb audio в комнатах — нужен ли spatial audio?
4. Animations Frost Goblin idle (между атаками) — 1 или несколько вариантов?
5. Ice Warden visual — полностью лёд или частично каменный?
6. Tile transitions между комнатами — fade-to-black 0.5 сек или physical door open?

---

## Implementation priority для prototype

1. **Frost Goblin** (basic enemy template, tutorial enemy для всех биомов)
2. **Hero auto-walk + auto-attack** logic
3. **Swipe-dodge** mechanic с side-step
4. **Burst slot activation** (1 burst type для start: Missile Strike)
5. **Артефакт drop + magnetism**
6. **Lobby merge UI** (3×3 grid)
7. **Wave transition** (room-to-lobby flow)
8. **Glacial Brute** (elite с AOE telegraph)
9. **Ice Spitter** (ranged enemy)
10. **Ice Warden boss** (4 phases)
