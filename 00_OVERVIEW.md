# ARTIFEX — Overview

**Жанр:** Idle RPG / Dungeon Crawler / Backpack Roguelike
**Камера:** First-person view из глаз героя
**Платформа:** Mobile (portrait)

---

## Концепт в одном предложении

Герой автоматически идёт вперёд по подземелью от первого лица — игрок управляет его рюкзаком: мержит артефакты в сетке, активирует burst-абилки и уклоняется от снарядов свайпами.

---

## Core Mechanic — две фазы

**Лобби-фаза (60 сек, между комнатами):**
Герой стоит на месте, игрок видит сетку рюкзака. Падают артефакты, игрок drag-drop их на сетку, мержит 3 одинаковых = усиленный артефакт. Выбирает 3 active slots для следующей комнаты.

**Боевая фаза (30-60 сек, в комнате):**
Камера от первого лица. Hero auto-walks вперёд по коридору. Враги появляются впереди и приближаются. Игрок:
- **Swipe влево/вправо** — side-step dodge от снарядов
- **Tap по врагу** — focus fire (hero auto-attacks ближайшего по умолчанию)
- **Tap по active slot** — burst (AOE / shield / heal)
- Артефакты падают с убитых врагов и автоматически летят в backpack

После убийства всех врагов в комнате → дверь открывается → возврат в лобби-фазу.

---

## Структура run'а

**Биом** = 10 комнат подряд (последняя — boss). 1 биом ≈ 5-10 минут.
**Run** = 3-5 биомов = 15-30 минут.

После каждого биома:
- Carry-over choice — игрок выбирает 3 артефакта взять с собой (остальное теряется)
- Event node — выбор из 3 опций (treasure / curse / vendor)

После смерти героя — return в Hub с Soul Points (meta currency).

---

## Сетка-рюкзак

- Стартовая форма: 3×3 на биоме 1 (Ice Castle)
- Каждый биом = новая форма сетки (3×4, T-shape, 4×4 с blocked corners, и т.д.)
- 3 школы артефактов: red (damage), blue (defense), green (sustain)
- Стаки артефактов в backpack автоматически активируют passive synergies при достижении порогов (1x / 2x / 3x / 4x / 5x)

---

## Документация (11 файлов, готово к разработке)

| # | Файл | Что описывает |
|---|---|---|
| 00 | OVERVIEW.md | Этот файл — high-level pitch и roadmap |
| 01 | CORE_LOOP.md | Цикл lobby ↔ combat, player inputs, end conditions |
| 02 | BIOME_01_ICE_CASTLE.md | Биом 1 детально — 10 комнат, враги, drops, Ice Warden boss |
| 03 | BACKPACK_SYSTEM.md | Grid mechanics, merge rules, 5 biome shapes, active slots, saturation prevention |
| 04 | SYNERGY_SYSTEM.md | 24 synergies — 3 schools × 5 + 4 cross + 5 tier-4, balance constraints |
| 05 | COMBAT_SYSTEM.md | First-person camera, swipe-dodge, 5 burst types, enemy AI, damage formula |
| 06 | HERO_BOARD_PROGRESSION.md | D1-D30 unlock schedule (mechanical, не stat bumps) |
| 07 | PRESTIGE_META.md | Prestige loop, Cosmic Shards earning + spending |
| 08 | ROGUELIKE_LAYER.md | Carry-over choice, event nodes, D25+ run modifiers |
| 09 | UI_UX_SPEC.md | 20 screens spec, touch targets, color palette, accessibility |
| 10 | TECH_REQUIREMENTS.md | Engine, target devices, performance budgets, networking, validation gates |
| 11 | ARTIFACTS_CATALOG.md | 20+ unique artifacts с active abilities, tier scaling T1-T5, drop tables per biome |
| 12 | FPV_VISUAL_PLAN.md | Implementation plan для FPV view в существующий HTML5 prototype |
| 13 | ASSET_GENERATION_PROMPTS.md | Image gen prompts (Midjourney/DALL-E) для backgrounds, hands, 5 weapons, размеры |
| 14 | CLAUDE_CODE_PROMPT.md | Phase 1 prompt для Claude Code — заменить background на layered art (sky+city+path) |
| 15 | LAYOUT_REWORK_PLAN.md | 5-phase план перехода на Hybrid idle layout (как Cold War Z RPG): inventory always visible, FPV combat 50% top, hands в bottom corners, redesigned HUD |

**Reading order для команды разработки:**

1. **Programmer / engineer**: 00 → 10 → 01 → 05 → 03 → 04
2. **Game designer / balance**: 00 → 01 → 03 → 04 → 06 → 07 → 08 → 02
3. **UI/UX designer**: 00 → 01 → 09
4. **Producer / PM**: 00 → 10 → 02 (vertical slice scope)
5. **Artist / 3D**: 00 → 02 → 09 → 10 (visual specs)
