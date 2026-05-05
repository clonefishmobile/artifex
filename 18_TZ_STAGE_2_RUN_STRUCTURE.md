# 18 — ТЗ Stage 2: Run Structure (Claude Code Prompt)

**Status:** Не запускать пока Stage 1 (17_TZ_STAGE_1) не завершён и протестирован.
**Дата:** 2026-05-05
**Версия архитектуры:** v2.3
**Iteration:** 2 / 3 (Vertical Slice)

---

## Что мы строим

Stage 2 vertical slice: добавляем **run structure** поверх готового combat'а из Stage 1. Это включает:
- Wave / Level / Biome иерархию (D27)
- XP system (kills + combo XP)
- Level-up choice (1 of 3 skill cards)
- 6 skill cards (3 universal + 3 type-restricted)
- Boss wave (3 phases по HP)
- Skill compatibility filter (раздел 4.2 синтеза)

## Что уже должно быть готово (после Stage 1)

✅ Backpack 5×6 с hero в центре
✅ 4 weapons с Influence patterns
✅ Slot machine 5 reels + combo detection
✅ Combat turn flow (spin → attack → enemy turn)
✅ Distance system + manual target switch
✅ Autobattle toggle
✅ Reroll system

## Что НЕ делаем в этой Stage

❌ HUB screens (Stage 3)
❌ Pre-run modifier choice (Stage 3)
❌ End-of-biome screen с meta gains (Stage 3)
❌ Loot drops в inventory (Stage 3)
❌ Multi-biome support (defer to Iteration 2)
❌ Skill rank progression (defer to Iteration 2)

---

## Prompt для Claude Code (copy-paste этот блок целиком)

```
Я работаю с проектом ARTIFEX — HTML5/Canvas 2D mobile game prototype.
Главный файл: /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Добавить run structure поверх готового combat'а из Stage 1. Это XP system, level-up choices, skill cards, wave/level/biome иерархию, boss wave.

ОБЯЗАТЕЛЬНО прочитай перед работой:
1. /Users/deniszabirohin/2andhalfgamer/ARTIFEX_REWORK_SYNTHESIS_v1.md — особенно разделы 4 (Skill System), 5.2 (Run structure), 6.3 (Combo XP), и D-list (D10, D25, D27).
2. /Users/deniszabirohin/2andhalfgamer/artifex_design/17_TZ_STAGE_1_CORE_COMBAT.md — для понимания, что уже сделано.

Combat должен уже работать end-to-end (Stage 1 завершён). Если нет — скажи мне, перед Stage 2 нужно закрыть Stage 1.

КОНКРЕТНО, пошагово:

STEP 1: Run hierarchy (D27)
Добавить state в G:
- `G.run = { biome: 'ice_castle', currentLevel: 1, currentWave: 1, totalLevelsInBiome: 3 }`
- `BIOME_DEFS` config:
```js
const BIOME_DEFS = {
  ice_castle: {
    name: 'Ice Castle',
    levels: 3, // early biome
    wavesPerLevel: 20,
    enemyTypes: ['ice_grunt', 'ice_archer', 'ice_brute'],
    bossType: 'ice_warden'
  }
};
```
- На старте run'а: `currentLevel=1, currentWave=1`
- После wave clear: increment currentWave; если 20 → level clear, increment currentLevel; если currentLevel > biomeDef.levels → biome clear (boss after)

STEP 2: Wave structure
- Wave duration: 5-10 секунд (1-2 turns)
- 1-3 врагов spawned per wave (varies by wave_id для variety)
- Wave clear когда все enemies dead
- После wave clear: 0.5 sec transition → next wave
- Если wave triggered level-up → pause до skill choice (см. STEP 4)

STEP 3: XP system (D10, D25)
Добавить state:
- `G.run.xp = 0`
- `G.run.level = 1`
- `G.run.xpToNext = 50` (level 1→2)

XP scaling:
```js
function xpThreshold(level) {
  return Math.floor(50 * Math.pow(1.5, level - 1));
  // 1→2=50, 2→3=75, 3→4=110, 4→5=165, 5→6=250
}
```

XP sources:
- **Kill XP**: small mob=1, elite=5, mini-boss=20, boss=50
- **Combo XP**: применяй формулу `5 × combo_multiplier × tier_multiplier`:
  - combo_multiplier: 2-of=1, 3-of=3, 4-of=6, 5-of=10
  - tier_multiplier: T1=1.0, T2=1.3, T3=1.7
  - Пример: 3-of-a-kind T3 bow → 5 × 3 × 1.7 = 25 XP

Anti-exploit (D25):
- Reroll НЕ начисляет XP (только финальный combo на attack)
- XP cap per turn: первое combo даёт full XP, повторные после reroll → 50%
- XP cap per wave: 200 XP max

При накоплении XP до threshold'а:
- Increment level
- Subtract threshold from XP
- Trigger level-up event (см. STEP 4)
- Update xpToNext = xpThreshold(newLevel)

XP bar UI: показать в combat screen (top), update real-time с smooth lerp animation.

STEP 4: Level-up Choice (раздел 3.4 синтеза)
- При triggered level-up:
  - Pause combat
  - Show modal с 3 random skill cards из owned pool (filtered by compatibility — см. STEP 5)
  - Player taps card → apply на selected weapon (или auto-apply на hero если universal/hero-only)
  - Resume combat
- Если у игрока < 3 owned cards — fill оставшиеся slots с placeholder cards (или генерируй random из default pool)
- Skill auto-attaches (без отдельного assignment screen — это в Stage 3)

STEP 5: Skill cards (6 для MVP)
Добавить SKILL_DEFS:
```js
const SKILL_DEFS = [
  {
    id: 'vampirism',
    name: 'Vampirism',
    tag: 'Damage',
    effect: 'lifesteal',
    value: 0.20,
    compatible: 'all',
    desc: '+20% lifesteal'
  },
  {
    id: 'pierce',
    name: 'Pierce',
    tag: 'Damage',
    effect: 'pierce',
    value: 1,
    compatible: 'precise', // works only on weapons with 'Precise' tag (Bow, Dagger)
    desc: '+1 enemy through (only Precise weapons)'
  },
  {
    id: 'critical',
    name: 'Critical',
    tag: 'Damage',
    effect: 'crit_chance',
    value: 0.20,
    compatible: 'all',
    desc: '+20% crit chance'
  },
  {
    id: 'frost',
    name: 'Frost',
    tag: 'Damage',
    effect: 'slow_on_hit',
    value: 0.50,
    compatible: 'ranged', // works only on weapons with 'Ranged' tag (Bow, Shotgun)
    desc: 'Slow enemies on hit (Ranged weapons)'
  },
  {
    id: 'sturdy',
    name: 'Sturdy',
    tag: 'Defense',
    effect: 'max_hp_pct',
    value: 0.20,
    compatible: 'hero',
    desc: '+20% Hero max HP'
  },
  {
    id: 'quickdraw',
    name: 'Quickdraw',
    tag: 'Utility',
    effect: 'reroll_cd',
    value: -0.20,
    compatible: 'hero',
    desc: '-20% reroll cooldown'
  }
];
```

State:
- `G.ownedSkillCards = ['vampirism', 'pierce', 'critical', 'frost', 'sturdy', 'quickdraw']` — все 6 в коллекции (для MVP, в финале unlock через progression)
- `G.activeSkills = []` — applied на weapons/hero в текущем run'е (теряется в end-of-biome)
- При apply skill: push в G.activeSkills с указанием на target weapon UID или 'hero'

Skill effect application:
- Skills run-only — теряются в end-of-biome (clear G.activeSkills)
- Skill VFX при weapon shot: уникальная color signature (vampirism = красная капля + heal pulse, pierce = projectile passes through, etc.)
- Damage numbers подкрашены под skill color если skill procced

STEP 6: Skill compatibility filter (раздел 4.2 синтеза)
- При level-up modal: показывать ТОЛЬКО compatible cards
- Compatibility check:
  - 'all' = always compatible
  - 'precise' = только если у player есть weapon с 'Precise' tag в backpack'е
  - 'ranged' = только если у player есть weapon с 'Ranged' tag
  - 'hero' = всегда compatible (apply на hero, не на weapon)
- Если нет compatible weapons для type-restricted skill — не предлагай эту card

STEP 7: Boss Wave (раздел 3.5 синтеза)
- Position: последняя (20-я) волна последнего уровня биома (Level 3 для Ice Castle)
- Boss type: 'ice_warden'
- HP = 5 × standard wave enemy HP
- 3 phases по HP%:
  - Phase 1 (100-66%): default attacks (single projectile + melee)
  - Phase 2 (66-33%): + AOE telegraph (2-sec warning), red circle на floor, dodge через target switch на дальнего врага
  - Phase 3 (33-0%): + 1-2 minion summons (basic ice_grunts), accelerated attack rate
- Death drop (placeholder в Stage 2, полный loot в Stage 3):
  - Display "Biome Complete!" message
  - Save to G.run.completed = true
  - Will trigger end-of-biome screen в Stage 3

STEP 8: Wave/Level transitions
- На wave clear: brief 0.5 sec animation + "Wave X/20" text overlay → next wave
- На level clear (after wave 20): "Level X Complete!" 1.5 sec banner → next level
- На biome complete (after boss): trigger end-of-biome flow (в Stage 3)

STEP 9: Combat UI updates
- Top status bar: "Biome: Ice Castle | Level 1/3 | Wave 5/20"
- XP bar visible (top, под status bar)
- Wave clear / level clear / boss явно communicated visually
- Level-up modal — full screen overlay с 3 cards и tap-to-select

ЧТО НЕ ТРОГАТЬ:
- Combat turn logic из Stage 1 (slot, reroll, target switch, autobattle)
- Influence System
- Distance system
- Backpack 5×6, hero, weapons
- Existing damage formula (только augment с skill effects)

ВЕРНИ:
1. Список изменённых функций
2. Список новых функций (XP, skills, level-up, boss)
3. Как тестировать (full biome run from start to boss)
4. Известные TODO (что отложил для Stage 3)

CONSTRAINTS:
- Vanilla JS, как в Stage 1
- Skill effects implemented модульно — каждый эффект как отдельная function (легко добавлять новые в будущем)
- XP system с cap и anti-exploit (D25)
- Skill compatibility filter работает корректно
- Boss 3 phases trigger по HP%, smooth transitions
```

---

## Что Claude Code должен сделать

После выполнения prompt'а ожидаемое состояние:

1. State `G.run` с biome/level/wave tracking
2. XP bar UI работает, обновляется с kills + combos
3. На level-up: modal с 3 skill cards, choice → apply на weapon/hero
4. Skill effects работают в combat (vampirism healing, pierce visual, frost slow и т.д.)
5. После 60 waves (3 levels × 20) — boss spawn, 3 phases, kill → "Biome Complete!"
6. Skill cards теряются на end-of-biome (placeholder сейчас, полный flow в Stage 3)

---

## Тестирование (Денис, после Claude Code)

1. Открой index.html, lobby → BATTLE
2. Combat начинается на Wave 1, Level 1, Ice Castle
3. Убивай enemies → XP накапливается (видно в bar)
4. Получи combo (3-of-a-kind) → видно XP gain «+25 XP»
5. На level-up: modal с 3 skill cards
6. Pick skill (например Vampirism на sword) → видишь heal effects при ударах
7. Дойди до Wave 20 → "Level 1 Complete!" → Wave 1 of Level 2
8. Дойди до Wave 20 of Level 3 → boss spawn
9. Boss проходит 3 phases (100-66, 66-33, 33-0)
10. Boss kill → "Biome Complete!" message

**Acceptance:**
- 3-5 level-ups per биом (если меньше/больше — adjust XP scaling)
- Skill compatibility filter работает (не предлагает Pierce если у игрока нет Precise weapon)
- Boss 3 phases visually distinct
- Combat turn duration ≤ 6 сек (даже с XP overlay и skill VFX)

---

## Reference

- **Architecture:** `/Users/deniszabirohin/2andhalfgamer/ARTIFEX_REWORK_SYNTHESIS_v1.md` (v2.3)
- **Stage 1 spec:** `17_TZ_STAGE_1_CORE_COMBAT.md`
- **Next Stage:** `19_TZ_STAGE_3_META_CONNECTION.md` (HUB, pre-run modifier, end-of-biome, loot)
