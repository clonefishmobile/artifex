# 19 — ТЗ Stage 3: Meta Connection (Claude Code Prompt)

**Status:** Не запускать пока Stage 2 (18_TZ_STAGE_2) не завершён и протестирован.
**Дата:** 2026-05-05
**Версия архитектуры:** v2.3
**Iteration:** 3 / 3 (Vertical Slice)

---

## Что мы строим

Stage 3 vertical slice: соединяем готовый combat + run structure с **meta loop** (HUB). Это включает:
- HUB minimal screens (backpack screen + start run button)
- Pre-run flow (preview + 1 of 3 modifier choice + loadout reveal anim)
- End-of-biome screen с visible meta-gains
- Loot drop logic (weapon shards, skill rank tokens)
- Per-run modifier pool (8 cards для MVP)

После Stage 3 у нас будет **полный playable vertical slice** — pre-run → combat → end-of-biome → back to HUB → start снова.

## Что уже должно быть готово (после Stage 1+2)

✅ Backpack 5×6 + hero + 4 weapons + Influence System
✅ Slot machine + combat turn flow
✅ Wave/Level/Biome иерархия
✅ XP system + level-up choice + 6 skill cards
✅ Boss wave (3 phases)

## Что НЕ делаем в этой Stage

❌ Multi-biome support (Iteration 2)
❌ Skill rank progression (Iteration 2)
❌ Skill assignment screen (apply ДО run'а — Iteration 2)
❌ Multi-hero classes (Iteration 2)
❌ IAP / monetization
❌ Daily quests / idle layer (Iteration 3+)
❌ Tutorial / onboarding (Iteration 3+)
❌ Polished VFX/sound

---

## Prompt для Claude Code (copy-paste этот блок целиком)

```
Я работаю с проектом ARTIFEX — HTML5/Canvas 2D mobile game prototype.
Главный файл: /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Подключить готовый combat + run structure к meta loop (HUB). Это HUB screens, pre-run flow, end-of-biome screen с loot, per-run modifier choice.

ОБЯЗАТЕЛЬНО прочитай перед работой:
1. /Users/deniszabirohin/2andhalfgamer/ARTIFEX_REWORK_SYNTHESIS_v1.md — особенно разделы 5.1 (pre-run), 5.3 (post-biome), 7.1 (Hub), 7.4 (per-run modifiers), и D-list (D2, D27).
2. /Users/deniszabirohin/2andhalfgamer/artifex_design/17_TZ_STAGE_1_CORE_COMBAT.md
3. /Users/deniszabirohin/2andhalfgamer/artifex_design/18_TZ_STAGE_2_RUN_STRUCTURE.md

Оба прошлых stage должны быть завершены и работать. Если нет — закрой их сначала.

КОНКРЕТНО, пошагово:

STEP 1: HUB screen layout
Создать новую HUB фазу (game state machine: HUB ↔ COMBAT). Existing lobby phase можно re-use или заменить.

HUB screens (минимум):
- **Backpack Screen** (главный, default open):
  - 5×6 grid с hero в центре (preview, не runtime)
  - Inventory panel сбоку — list of owned weapons (drag из inventory в grid)
  - Tap weapon → info card с stats, tags, influence pattern preview
  - Tap hero → info card с zone effect
  - Большая кнопка "START RUN" внизу
- **Inventory** (можно совместить с backpack screen в side panel)
- **Settings** (минимально):
  - Audio toggle (BGM on/off, SFX on/off)
  - "Show all zones" toggle (advanced visualization mode)
  - Reset Run (debug button)

UI: tab-bar внизу с 2 tabs (Backpack | Settings) или single-screen layout.

STEP 2: Pre-run Flow (раздел 5.1 синтеза)
Когда player жмёт "START RUN":
1. Показать **Pre-run Preview screen** (~5 sec):
   - Biome name + thumbnail (Ice Castle illustration / placeholder)
   - Enemy types preview (3-4 icons: ice_grunt, ice_archer, ice_brute)
   - **Choose 1 of 3 modifier cards** (random из pool — см. STEP 3)
2. После modifier choice → **Loadout Reveal animation** (~2 sec):
   - Backpack icons "вылетают" и встают в slot reels (показывает column → reel mapping)
   - Hero подсвечен в центре с его pattern visible
3. Combat начинается (Wave 1 of Level 1)

STEP 3: Per-run Modifier Pool (раздел 7.4 синтеза, 8 cards для MVP)
```js
const MODIFIER_DEFS = [
  { id: 'lucky_start', name: 'Lucky Start', desc: '+1 starting skill drop', tone: 'easy' },
  { id: 'combo_master', name: 'Combo Master', desc: '+30% combo XP', tone: 'medium' },
  { id: 'glass_cannon', name: 'Glass Cannon', desc: '+50% damage, -30% HP', tone: 'risk' },
  { id: 'iron_will', name: 'Iron Will', desc: '-20% damage taken', tone: 'easy' },
  { id: 'free_reroll', name: 'Free Reroll', desc: 'Free reroll каждые 3 turns', tone: 'medium' },
  { id: 'xp_boost', name: 'XP Boost', desc: '+20% kill XP', tone: 'easy' },
  { id: 'trader', name: 'Trader', desc: 'Enemies +30% HP, loot ×2', tone: 'risk' },
  { id: 'skill_hoarder', name: 'Skill Hoarder', desc: 'Choose 1 of 4 на level-up', tone: 'easy' }
];
```

Implement effects:
- `lucky_start`: trigger 1 free skill drop в первой волне
- `combo_master`: при combo XP — apply ×1.3 multiplier
- `glass_cannon`: hero damage ×1.5, hero max HP ×0.7
- `iron_will`: incoming damage to hero ×0.8
- `free_reroll`: дополнительный reroll каждые 3 turns
- `xp_boost`: kill XP ×1.2
- `trader`: enemy HP ×1.3, loot drops ×2
- `skill_hoarder`: level-up choice показывает 4 вместо 3 cards

State:
- `G.run.modifier = null` (set on choice)
- На run start: pick 3 random modifiers из pool, show selection
- Player taps card → set `G.run.modifier = card.id`
- Apply effect on combat начале

STEP 4: Loot Drop Logic (раздел 4.4 ТЗ Iteration 1)
Per wave clear:
- 1-3 gold (small amount)
- ~10% chance: 1 weapon shard (биом-specific, для Ice Castle = sword/dagger shards)

Per level clear:
- 5 weapon shards (биом-specific)
- 1 skill rank token

Per boss kill (guaranteed):
- 1 rare skill card duplicate (random из owned pool)
- 5 cosmic shards
- 10+ Ice Castle shards (sword, dagger, bow)

State:
```js
G.inventory = {
  gold: 0,
  cosmicShards: 0,
  weaponShards: { sword: 0, bow: 0, shotgun: 0, dagger: 0 },
  skillRankTokens: 0,
  skillCardDupes: { vampirism: 0, pierce: 0, ... } // counts of duplicates
};
```

Loot всё persistent (saves to localStorage).

STEP 5: End-of-Biome Screen (раздел 4.5 ТЗ + 5.3 синтеза)
После boss kill:
1. Boss death animation
2. "BIOME COMPLETE!" overlay
3. Show end-of-biome screen:
   - Run Stats:
     - Time: Xm Xs
     - Kills: count
     - Combos triggered: count
     - Skills picked: count
     - Best combo: «4-of-a-kind ×2.5»
   - **Meta Gains** (видимое накопление, animated count-up):
     - +X hero XP
     - +X Sword shards
     - +X Bow shards
     - +X Skill rank tokens
     - +X Vampirism duplicate (если выпал)
     - +X Cosmic shards
   - Button: [BACK TO HUB]

При нажатии:
- Skills из run'а лосятся (clear G.activeSkills)
- Run-state XP обнуляется
- Loot persistent (сохраняется в G.inventory)
- Возврат в HUB → backpack screen

STEP 6: Save/Load (D2 — backpack persistent)
- Сохранять в localStorage:
  - `G.backpack` (placement weapons в grid)
  - `G.inventory` (gold, shards, etc.)
  - `G.ownedSkillCards`
  - `G.settings` (audio, show zones toggle)
- Не сохранять: `G.run.*` (run-state — теряется при reset)
- Load на startup: hydrate backpack, inventory, settings
- Edge case: первый запуск — initialize defaults (стартовый backpack с 1-2 weapons, например 1 sword + 1 bow)

STEP 7: Game state machine update
```
APP_START
  ↓
[load saved state]
  ↓
HUB → tap "START RUN" → PRE_RUN
  ↓
PRE_RUN → modifier choice → LOADOUT_REVEAL → COMBAT
  ↓
COMBAT → boss kill → END_OF_BIOME
  ↓
END_OF_BIOME → tap "BACK TO HUB" → HUB
```

State machine variable: `G.phase = 'hub' | 'pre_run' | 'loadout_reveal' | 'combat' | 'end_of_biome'`

STEP 8: HUB screen UI
Layout (portrait mobile):
```
┌─────────────────────────────────┐
│ [Settings ⚙]  ARTIFEX  [Inv 📦] │  ← top bar
├─────────────────────────────────┤
│                                 │
│   ┌───────────────────────┐     │
│   │  BACKPACK 5×6         │     │
│   │  with hero in center  │     │
│   │  (drag-drop weapons)  │     │
│   └───────────────────────┘     │
│                                 │
│   Tap weapon → info card        │
│                                 │
│   ┌─ Inventory panel ──┐        │
│   │ [Sword ×2] [Bow]   │        │
│   │ [Dagger]   [Shot]  │        │
│   └────────────────────┘        │
│                                 │
│  Currency: 🪙 250 💎 5 🔧 3     │
│                                 │
│  [ START RUN ]                  │
└─────────────────────────────────┘
```

ЧТО НЕ ТРОГАТЬ:
- Combat logic (Stage 1)
- Run structure / XP / level-up / skills (Stage 2)
- Boss wave logic
- Influence System
- Slot machine

ВЕРНИ:
1. Список изменённых функций
2. Список новых функций (HUB UI, modifier system, end-of-biome screen, save/load)
3. Полный playthrough test (HUB → pre-run → combat → boss → end-of-biome → HUB)
4. Известные TODO для Iteration 2/3

CONSTRAINTS:
- Vanilla JS, как раньше
- localStorage для persistent data (no backend)
- Все UI screens responsive под portrait mobile
- Animations smooth (no jank)
- Graceful fallback если localStorage unavailable
```

---

## Что Claude Code должен сделать

После выполнения prompt'а ожидаемое состояние:

1. HUB screen с backpack 5×6, inventory panel, currency display, "Start Run" button
2. Pre-run flow: preview → modifier choice (3 random из 8 pool) → loadout reveal anim → combat
3. Modifier effects работают в combat (8 разных effects)
4. Loot drops по wave/level/boss
5. End-of-biome screen с stats + meta gains + count-up animations
6. localStorage persistence для backpack, inventory, settings
7. Game state machine: HUB ↔ COMBAT works end-to-end

---

## Тестирование (Денис, после Claude Code)

1. Открой index.html → загружается HUB screen с backpack
2. Drag weapons в backpack, видны influence patterns при tap'е
3. Click "START RUN"
4. Pre-run preview: видно biome thumbnail + enemy icons + 3 modifier cards
5. Pick modifier (например "Combo Master") → loadout reveal anim
6. Combat начинается, modifier effect активен (combo XP boosted)
7. Дойди до boss, kill
8. End-of-biome screen: видишь run stats + meta gains
9. Click "BACK TO HUB" → возврат в HUB
10. Inventory updated (видны новые shards, gold, etc.)
11. Restart игру (refresh браузера) → backpack и inventory сохранились (localStorage)

**Acceptance:**
- Полный run cycle работает без crashes (~5-10 минут)
- Modifier effect заметно меняет run feel (например Glass Cannon делает hero squishy)
- End-of-biome screen показывает >2 видимых meta-gain'а
- localStorage persistence работает (backpack сохраняется)

---

## Reference

- **Architecture:** `/Users/deniszabirohin/2andhalfgamer/ARTIFEX_REWORK_SYNTHESIS_v1.md` (v2.3)
- **Stage 1 spec:** `17_TZ_STAGE_1_CORE_COMBAT.md`
- **Stage 2 spec:** `18_TZ_STAGE_2_RUN_STRUCTURE.md`
- **TZ Iteration 1 (full):** `/Users/deniszabirohin/2andhalfgamer/ARTIFEX_TZ_ITERATION_1.md`
- **Battle Flow Brief:** `/Users/deniszabirohin/2andhalfgamer/ARTIFEX_BATTLE_FLOW_BRIEF.md`
