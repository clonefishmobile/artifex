# 17 — ТЗ Stage 1: Core Combat (Claude Code Prompt)

**Status:** Ready to ship. Скопируй prompt ниже в Claude Code и запусти.
**Дата:** 2026-05-05
**Версия архитектуры:** v2.3
**Iteration:** 1 / 3 (Vertical Slice)

---

## Что мы строим

Stage 1 vertical slice: переписываем core combat под новую архитектуру (synthesis v2.3). Заменяем старую систему синергий + body-parts targeting на:
- Backpack 5×6 grid с hero в центре
- 4 weapons с фиксированными footprint'ами и Influence Pattern'ами
- Slot machine 5 reels = 5 columns backpack'а
- Combat turn: spin → combo → (optional reroll/target switch) → attack → enemy turn
- Distance system (close/mid/far) + sharp dropoff curves
- Autobattle toggle

## Что уже есть в коде (current state)

- `index.html` — single-file HTML5/Canvas 2D mobile prototype (vanilla JS)
- Grid system: `GCOLS=5, GROWS=7` (нужно сжать до 5×6)
- Старая synergy система (red/blue/green schools) — **удаляем**
- Body-parts targeting (BODY_PARTS array) — **удаляем**
- Slot machine из 16-го дока — **переписываем под 5 reels**
- Weapon defs в `WEAPON_DEFS` — **расширяем**, добавляя Influence Pattern field

## Что НЕ делаем в этой Stage

❌ XP system, level-ups, skill cards (Stage 2)
❌ Wave/Level/Biome иерархия (Stage 2)
❌ Boss waves (Stage 2)
❌ HUB screens, pre-run modifier (Stage 3)
❌ End-of-biome screen, loot drops (Stage 3)
❌ Polished VFX/sound (placeholder OK)

---

## Prompt для Claude Code (copy-paste этот блок целиком)

```
Я работаю с проектом ARTIFEX — HTML5/Canvas 2D mobile game prototype.
Главный файл: /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Переписать core combat под новую архитектуру (Stage 1 of 3 итерации). Это включает: новый backpack 5×6 с hero, новые weapons с influence patterns, переписанный slot machine, новый combat turn flow, distance system.

ОБЯЗАТЕЛЬНО прочитай перед работой:
1. /Users/deniszabirohin/2andhalfgamer/ARTIFEX_REWORK_SYNTHESIS_v1.md — полная архитектура (v2.3). Особенно разделы 3.3, 3.4, 3.4.1, 3.5, 3.6, 3.7, 6.1-6.4, и D-list (1-29).
2. /Users/deniszabirohin/2andhalfgamer/ARTIFEX_BATTLE_FLOW_BRIEF.md — короткая схема flow.

КОНКРЕТНО, пошагово:

STEP 1: Backpack 5×6 (D2)
- Изменить `const GCOLS=5, GROWS=7` → `const GCOLS=5, GROWS=6`
- Проверить что всё, что использует GROWS, продолжает работать (grid render, placement validation, slot reading)
- Backpack по-прежнему persistent (не сбрасывается)

STEP 2: Hero в центре (D19, D21, D28, D29)
- Добавить новый объект HERO в state:
  - Footprint 2×2 в cells (col=2, row=2) до (col=3, row=3)
  - Influence Pattern: 8-cell ring around (соседние ячейки 8-направлений)
  - Zone Effect: «+20% ATK для всех weapons in zone»
  - HP base = 100
  - НЕ имеет отдельных strikes (атакует только через weapons)
- Hero не двигается, не сменяется, не появляется в slot reels
- При расчёте placement — hero блокирует свои 4 cells (как locked cells)

STEP 3: Удалить устаревшие системы
УДАЛИ:
- `SCHOOLS`, `SCHOOL_COLORS`, `SCHOOL_ICONS`, `SCHOOL_GLOWS` (old synergy теги)
- `BODY_PARTS` array и всю body-parts targeting логику
- `recalcSynergies()`, `synDmgBonus()`, `synMaxHpBonus()`, `synDR()`, `synRegen()`, `synVamp()` — старая synergy система
- `BIOME_POOL`, `BIOME_WEIGHTS` — старые drop pools (заменим в Stage 2)

STEP 4: Новые weapon types (D5, 4 weapons для MVP)
Заменить `WEAPON_DEFS` на:
```js
const WEAPON_DEFS = [
  {
    id: 'sword',
    name: 'Sword',
    footprint: {w:1, h:1},
    tags: ['Melee'],
    baseDmg: 100,
    cd: 1.0,
    influencePattern: '4_cross',  // 4 cells: top, bottom, left, right
    zoneEffect: { type: 'atk_per_match', tag: 'Sword', value: 0.15 },
    distanceCurve: { close: 1.0, mid: 0.75, far: 0.50 },
    perks: { 1: null, 2: 'cleave_1', 3: 'cleave_2' }
  },
  {
    id: 'bow',
    name: 'Bow',
    footprint: {w:3, h:1},
    tags: ['Ranged','Precise'],
    baseDmg: 100,
    cd: 1.5,
    influencePattern: '6_wide_row',  // 6 cells: 3 above + 3 below
    zoneEffect: { type: 'crit_per_match', tag: 'Precise', value: 0.05 },
    distanceCurve: { close: 0.30, mid: 0.80, far: 1.0 },
    perks: { 1: null, 2: 'pierce_1', 3: 'pierce_2' }
  },
  {
    id: 'shotgun',
    name: 'Shotgun',
    footprint: {w:2, h:2},
    tags: ['Ranged','Heavy'],
    baseDmg: 100,
    cd: 1.8,
    influencePattern: '4_adjacent',  // 4 cells: top, bottom, left, right (но от bounding box 2x2)
    zoneEffect: { type: 'atk_per_match', tag: 'Heavy', value: 0.10 },
    distanceCurve: { close: 1.0, mid: 1.0, far: 0.30 },
    perks: { 1: null, 2: 'aoe_radius_1', 3: 'aoe_radius_2' }
  },
  {
    id: 'dagger',
    name: 'Dagger',
    footprint: {w:1, h:1},
    tags: ['Melee','Precise'],
    baseDmg: 80,
    cd: 0.8,
    influencePattern: '4_adjacent',
    zoneEffect: { type: 'crit_per_match', tag: 'Sword', value: 0.08 },
    distanceCurve: { close: 1.0, mid: 0.70, far: 0.40 },
    perks: { 1: null, 2: 'bleed', 3: 'bleed_strong' }
  }
];
```

STEP 5: Tier scaling (D24, D26 — non-negotiable ×2.5)
- Заменить `tierEffMul`: `[1, 2.5, 6.25, 15.6, 39.0]` (×2.5 per tier)
- Footprint фиксирован per type, НЕ растёт с tier'ом (D5)
- Merge правила: 2× same type+tier, adjacent → 1× tier+1, footprint остаётся той же формы
- Tier-up активирует weapon perk (T2 = 'cleave_1', T3 = 'cleave_2', etc.) — perks применяются в combat damage

STEP 6: Influence System (D20, D22 — раздел 3.6 синтеза)
Создать новые функции:
- `getInfluenceCells(weapon)` — возвращает array of {col,row} cells, которые попадают в weapon's zone
- `computeInfluenceBuff(weapon)` — итерирует все weapons в backpack'е, проверяет, попадает ли target weapon в их zone, и возвращает суммарный buff multiplier для каждого stat'а
- `computeFinalStats(weapon)` — base × tier × influenceBuff = pre-run stats
- Hero zone = 8-cell ring, дает «+20% ATK» (универсальный buff)

STEP 7: Slot Machine (D6, переписать из 16_COMBAT_REWORK_SPEC.md)
- 5 reels = 5 columns backpack'а
- Reel содержит unique weapons, чьи cells пересекают данный column
- Например: Bow занимает row=0, cols=0,1,2 → присутствует в reels 0, 1, 2
- На каждый turn: spin → stop sequentially (~300ms gap) → show weapons
- Combo detection: 2+ same UID в reels'ах одного turn'а:
  - 2-of-a-kind: ×1.3 dmg
  - 3-of-a-kind: ×1.8 dmg
  - 4-of-a-kind: ×2.5 dmg
  - 5-of-a-kind: ×4 dmg

STEP 8: Combat Turn Flow (раздел 6.4 синтеза)
Переписать combat-turn под новую логику:
```
turn_start():
  spinSlots()  -- 5 reels animate
  detectCombo() -- compute combo multiplier
  
  player_action_phase():
    if reroll_pressed and reroll_charges > 0: spinSlots()
    if enemy_tapped: switchTarget(enemy)
  
  attack_phase():
    if autobattle or attack_pressed:
      for weapon in reels:
        damage = computeDamage(weapon, target.distance)
        applyDamage(target, damage)
  
  enemy_phase():
    for enemy with attack_timer expired:
      damageHero(enemy.attack)
  
  checkWaveClear()
```

STEP 9: Inputs (D14)
- **Tap Attack** — продвигает turn (если autobattle off)
- **Autobattle toggle** — галочка под Attack кнопкой, default OFF, persistent в state
- **Tap по врагу** — manual target switch
- **Tap Reroll** — пере-крутить slot, использует charge
- Reroll НЕ даёт XP, XP cap per turn (но XP пока не имплементирован — сделаем в Stage 2, оставь TODO comment)

STEP 10: Distance System (D23, Q3.β — sharp curve)
- Враги на 3 distance layers (close/mid/far) — visualize в combat layout
- Auto-target = nearest enemy (default)
- Manual target switch via tap по врагу
- damage = base × tier × influence × combo × distanceCurve[target.distance]
- Close-zone enemy имеет attack_timer (3-5 секунд) — если не убит, hits hero

STEP 11: UI Layout (combat screen)
- Top section: 3 distance layers с врагами (close/mid/far visible)
- Mid: HERO HP bar
- Bottom: 5 slot reels horizontally
- Buttons: [Reroll N/M] [☑ Autobattle toggle] [ATTACK]
- (XP bar — placeholder, заполним в Stage 2)

STEP 12: Backpack visualization (D8 — Q12 reveal-on-tap)
- Default: zones скрыты
- Tap on item → его Influence Pattern подсвечивается cyan, affected items с зелёными arrows + floating numbers
- Drag from inventory → preview pattern над cells под cursor'ом

ЧТО НЕ ТРОГАТЬ:
- BGM, sfx (sfxHit, sfxCrit и т.д.) — оставь как есть
- Asset preloader, image rendering для backgrounds
- Camera shake, projectiles, basic enemy rendering
- Game state machine (lobby → combat → result) — структура остаётся, только внутри combat фазы переписываем
- Save/load logic если есть

ВЕРНИ:
1. Список всех изменённых функций
2. Список новых функций
3. Список удалённых функций
4. Как тестировать (steps for Денис)
5. Известные TODO (что отложил для Stage 2/3)

CONSTRAINTS:
- Vanilla JS, как в существующем файле
- Сохрани code style (no frameworks, no minification)
- Preserve все relevant comments / structure
- Если возникает конфликт между старой и новой логикой — оставь TODO comment, не ломай build
- Проверь что игра запускается после изменений (open index.html в браузере, lobby → combat должна работать end-to-end)
```

---

## Что Claude Code должен сделать

После выполнения prompt'а ожидаемое состояние:

1. `GCOLS=5, GROWS=6` (изменено)
2. Удалены: SCHOOLS, BODY_PARTS, recalcSynergies, синонимы (см. STEP 3)
3. Добавлен: HERO объект в state, занимает center 2×2
4. Расширен `WEAPON_DEFS` до 4 типов с influencePattern + zoneEffect + distanceCurve + perks
5. Новые функции: `getInfluenceCells`, `computeInfluenceBuff`, `computeFinalStats`, `getReelWeapons` (column-based), `detectCombo`, `computeDamage`
6. Slot machine: 5 reels, sequential stop, combo detection
7. Combat UI: 3 distance layers, slot reels, [Reroll] [Autobattle] [Attack] buttons
8. Reveal-on-tap influence visualization
9. Game запускается, lobby → combat → enemies appear → slot spin → attack → damage → enemy turn → repeat

---

## Тестирование (Денис, после Claude Code)

1. Открой `/Users/deniszabirohin/2andhalfgamer/artifex_design/index.html` в браузере
2. Lobby phase: backpack 5×6 видна, hero в центре (2×2), 4 типа weapons в inventory
3. Drag weapons в backpack — должны корректно вставать вокруг hero
4. Tap weapon → подсвечивается influence pattern + affected weapons
5. Click "BATTLE"
6. Combat phase:
   - Видны 3 distance layers с врагами (close/mid/far)
   - Slot reels внизу, 5 reels
   - [Reroll N/M] [☑ Autobattle] [ATTACK] кнопки видны
7. Tap "ATTACK" → slot crутится → стопится → combo detected (если есть) → damage наносится
8. Tap по врагу → target меняется
9. Включи Autobattle → атаки идут автоматически
10. Tap Reroll → slot перекрутился (если есть charge)
11. Кончатся враги → wave clear (placeholder, в Stage 2 будет полный wave/level/biome flow)

**Если что-то ломается** — скинь Claude Code error log, доработаем.

---

## Reference

- **Architecture:** `/Users/deniszabirohin/2andhalfgamer/ARTIFEX_REWORK_SYNTHESIS_v1.md` (v2.3)
- **Battle Flow:** `/Users/deniszabirohin/2andhalfgamer/ARTIFEX_BATTLE_FLOW_BRIEF.md`
- **Next Stage:** `18_TZ_STAGE_2_RUN_STRUCTURE.md` (XP, level-ups, skill cards, biome structure)
