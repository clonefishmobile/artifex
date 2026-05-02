# 01 — Core Gameplay Loop

**Камера:** First-person view (из глаз героя). Видим dungeon коридор перед собой, оружие/руки героя в нижней части экрана, врагов впереди.

---

## 1. Цикл одной комнаты (room cycle)

```
LOBBY PHASE (60 sec)
   ↓
COMBAT PHASE (30-60 sec, в комнате)
   ↓
ROOM CLEARED → дверь открывается
   ↓
LOBBY PHASE (next room)
```

10 комнат подряд = биом. После 10-й комнаты (boss) → carry-over → event node → следующий биом.

---

## 2. LOBBY PHASE (60 секунд)

### 2.1 Что игрок видит

Герой стоит перед закрытой дверью. На переднем плане — UI рюкзака:
- 3×3 сетка артефактов (форма зависит от биома)
- Очередь падающих артефактов сверху (6 видимых)
- 3 active slot'а внизу экрана (выделены золотым)
- Counter синергий справа («Red 2/3» / «Blue 1/3» и т.д.)
- Кнопка **Skip** (доступна после первых 15 сек)
- Таймер 60 сек справа сверху

### 2.2 Спавн артефактов

- Очередь из 6 артефактов сверху сетки
- Новый артефакт падает в очередь каждые **12 ± 2 сек**
- Игрок drag'ает артефакт из очереди в свободную ячейку сетки
- Drop chance per school: зависит от биома (см. 02_BIOME_01)
- Если backpack полон — следующий артефакт ждёт в очереди

### 2.3 Merge правила

- **2 одинаковых** артефакта (тот же type + tier) → merge в **tier+1** того же type
- Tier progression: 2×T1 → 1×T2, 2×T2 → 1×T3, 2×T3 → 1×T4, 2×T4 → 1×T5
- **Tier max: 5** (далее merge невозможен)
- Adjacency: горизонталь, вертикаль, диагональ (8 направлений) — артефакты должны касаться друг друга
- Анимация merge: 0.6 сек — два артефакта летят к свободной cell → flash → 1 tier+1 артефакт
- Audio: ascending chime
- **EXP начисление**: каждый merge на свободную cell → +N EXP (анимация частиц XP летящих в верхний progress bar). См. секцию 2.7 для EXP system.
- Edge case: если все 8 cells вокруг merge points заняты — merge не происходит, артефакты остаются. Для merge нужна минимум 1 свободная cell для результата.

### 2.4 Synergy triggers (passive)

Стаки артефактов в backpack (включая active slots) автоматически активируют synergies при достижении порогов:

- **1× red** = +5% damage (всегда активна)
- **2× red** = "Crimson Aegis" — 10% vampirism on hit
- **3× red** = "Pyromancer Combo" — 25% AOE flame on crit
- **4× red** = "Inferno Chain" — crits chain to next enemy (max 3)
- **5× red** = "Hellfire Storm" — every 5th hit AOE pulse (max 1 trigger per 5 hits)
- (Аналогично для blue / green — детали в `03_SYNERGY_SYSTEM.md`)

UI показывает прогресс к следующему порогу: «Red 2/3 — Pyromancer in 1». Когда новая synergy unlocks — золотой badge + sound cue.

### 2.5 Active slot system

3 active slot'а внизу экрана. Каждый артефакт **имеет свою уникальную активную способность** (active ability) — это не generic burst type. Поместив артефакт в slot, игрок получает доступ к его конкретной ability в combat.

- Tap по артефакту в backpack → toggle в active slot
- Visual: золотая рамка вокруг slotted артефактов
- Max 3 active slots. Tap 4-го → заменяет oldest selection
- Auto-locks когда лобби-таймер кончается
- **Active ability** = персональный скилл артефакта (cooldown, effect, animation). Tier артефакта влияет на силу ability:
  - T1 артефакт: weak version (~50% effect)
  - T3 артефакт: standard version (100%)
  - T5 артефакт: max version (~200% effect, reduced cooldown)
- Артефакт также **продолжает контрибутить в passive synergies** будучи в active slot (не теряет stack count)
- Полный каталог артефактов с их abilities — см. `11_ARTIFACTS_CATALOG.md`

### 2.6 EXP / Hero Level Progression

Каждый успешный merge на свободную cell начисляет **EXP** в hero level progression bar.

- **EXP бар** виден сверху лобби-UI (тонкая горизонтальная полоска под status bar)
- **EXP per merge** (в зависимости от tier результата):
  - 2× T1 → T2: **+5 EXP**
  - 2× T2 → T3: **+15 EXP**
  - 2× T3 → T4: **+45 EXP**
  - 2× T4 → T5: **+135 EXP**
- **Анимация**: после merge — частицы EXP (золотые искры) летят от merge point к EXP bar сверху (0.8 sec animation)
- **Hero level up** при заполнении бара:
  - Level up → +5% hero max HP, +2% damage, +1 small bonus (random or chosen)
  - Visual: golden flash на heroе + "LEVEL UP!" текст
  - Audio: triumphant chime
- **Level scaling**: каждый level требует +20% EXP (Level 1 = 100 EXP, Level 2 = 120 EXP, Level 3 = 144 EXP, …)
- **Reset**: hero level сбрасывается на end of run. Накопленный EXP переходит в Soul Points (1 level = 50 Soul Points bonus).

EXP bar также виден в combat phase (top of screen, тонкая полоска) — EXP не начисляется в combat, только в lobby при merge.

### 2.7 Skip и timer

- Manual skip кнопка enabled через 15 сек после старта lobby (anti-skip-spam)
- Auto-warning на 10 сек (orange flash таймера)
- Auto-warning на 5 сек (red beep)
- В 0 сек: дверь автоматически открывается → COMBAT PHASE

---

## 3. COMBAT PHASE (30-60 секунд)

### 3.1 Что игрок видит

First-person view:
- Перед heroем — коридор/комната с врагами (1-10 enemies depending on wave)
- Внизу экрана — оружие героя (анимация атаки), HP bar, 3 active slot icons (с cooldown rings)
- Враги отображены spritesами/3D models в перспективе — ближайшие крупнее, дальние меньше
- HP bars над врагами

### 3.2 Hero auto-walk

- Hero автоматически идёт вперёд со скоростью 2 tiles/sec (если ничего не блокирует)
- Останавливается если враг в melee range (1 tile спереди) — атакует ближайшего
- Auto-attack: ~1 удар в секунду, target = ближайший враг по дефолту

### 3.3 Player inputs в combat

**Swipe LEFT / RIGHT** — side-step dodge:
- Threshold: минимум 40px по горизонтали
- Distance: 1 tile в сторону
- Cooldown: 0.3 сек между dodges
- Forgiveness window: ±0.5 tile hit-detection
- Используется для уклонения от ranged projectiles врагов

**Tap по врагу** — focus fire:
- Hero переключает auto-attack target на этого врага
- Visual: красный crosshair над выбранным врагом
- Useful когда нужно сначала убить ranged unit, потом мили

**Tap по active slot icon** — burst activation:
- Slot 1, 2, 3 (внизу экрана)
- Когда CD активен — иконка серая с countdown
- При активации — burst срабатывает мгновенно

### 3.4 Active abilities (artifact-specific)

Каждый артефакт имеет **свою уникальную активную способность** — нет fixed burst types. Игрок выбирает 3 артефакта в active slots, и в combat доступны именно их abilities.

Примеры (полный каталог в `11_ARTIFACTS_CATALOG.md`):

| Артефакт (T3 standard) | School | Active ability | CD |
|---|---|---|---|
| **Crimson Core** | Red | Missile Volley — 3 projectiles, 2.5× damage | 8 сек |
| **Frostbite Sigil** | Blue | Frost Barrier — +40% HP shield 5 сек | 12 сек |
| **Verdant Spore** | Green | Plague Swarm — 3 poison drones 8 сек | 15 сек |
| **Bloodforge** | R+G hybrid | Berserk — +80% damage 3 сек, -10% HP | 20 сек |
| **Glacial Hourglass** | B + любой | Time Warp — freeze всех 2 сек | 25 сек |
| **Stormcaller** | Red T4 | Chain Lightning — 5 jumps, 3× damage | 18 сек |
| **Aegis Wreath** | Blue T4 | Sanctuary — full heal + invuln 3 сек | 35 сек |
| ... | ... | ... | ... |

**Tier scaling**: T1 артефакт даёт weakened version ability (~50% effect), T3 — standard, T5 — empowered (~200% effect, reduced cooldown). Например T1 Crimson Core = 2 projectiles 1.25× damage; T5 Crimson Core = 5 projectiles 4× damage.

**Активация**: tap по slot icon → ability triggers instantly. Cooldown ring появляется на icon.

### 3.5 Враги и их поведение

**Базовый враг (Basic):** auto-walks к hero, melee attack при контакте.
**Ranged враг:** стоит на месте, стреляет projectiles. Side-step dodge нужен.
**Elite враг:** 2× HP, 1.5× damage, может иметь special ability.
**Boss:** см. секцию 4.

Spawn в комнате: враги появляются впереди (на дальнем end of corridor) и приближаются. Можно spawn'ить групками (2-3 врага одновременно).

### 3.6 Артефакт drop

- 40-50% chance на kill (биом-dependent)
- При смерти врага — артефакт парит в воздухе → magnetism range 3 tiles → летит к heroу → автоматически уходит в backpack
- Tier dropped: weighted (биом-specific distribution, например Ice Castle 60% tier-1, 30% tier-2, 10% tier-3)
- Если backpack полон — артефакт остаётся на полу, despawn через 5 сек

### 3.7 Passive synergy procs в combat

Synergies из backpack автоматически работают:
- На начало wave — checked один раз (e.g. "Crimson Aegis активирована" badge)
- Каждые 5 сек — re-check (если стаки изменились через drops)
- Visual feedback: проявление эффекта на hero/enemy (FX particles, damage numbers)
- Лимиты proc rate (Hellfire Storm — max 1 per 5 hits, max 3 stacks одновременно)

### 3.8 Combat end conditions

- **Все враги убиты** → ROOM CLEARED → дверь открывается → next LOBBY phase
- **Hero HP = 0** → fail state
  - Опция: Emergency Revive ($1.99 IAP / RV ad) — restore +50% HP, max 1 раз в день
  - Без revive → end of run
- **Time limit 120 сек** (hard cap) — если враги ещё живы, force-spawn ослабленных врагов или auto-fail

---

## 4. BOSS ROOM (10-я комната биома)

### 4.1 Структура

Boss room — большая открытая комната, перспектива открывается шире. Boss появляется в центре, hero автоматически идёт к нему.

### 4.2 3 фазы

| Phase | HP threshold | Behavior |
|---|---|---|
| Phase 1 | 100-75% | Standard attacks, slow projectiles |
| Phase 2 | 75-50% | Adds AOE telegraph attacks (red zones показывают где взорвётся через 2 сек) |
| Phase 3 | 50-25% | Summons add minions (2 basic enemies на 25%) |
| Phase 4 | 25-0% | Berserk mode — accelerated patterns, агрессивное movement |

### 4.3 Boss kill

- Drop: гарантированный tier-4 артефакт
- Bonus: 50 gold + 1 Cosmic Shard fragment
- Затем → CARRY-OVER SCREEN

---

## 5. CARRY-OVER SCREEN (после boss kill)

Игрок видит свой текущий backpack (9 артефактов в стандартном случае). UI:
- Каждый артефакт можно tap'нуть → выделяется (golden border) = mark for carry
- Max 3 артефакта по дефолту
- "Watch Ad +1 carry slot" — RV кнопка, доступна 1 раз в день
- Confirm кнопка → next BIOME
- Auto-confirm через 15 сек если игрок не выбрал

Не-carried артефакты теряются. Конвертируются в Soul Points (10 за артефакт).

---

## 6. EVENT NODE (между биомами)

3 карты revealed (random выбор):

| Event | % spawn | Effect |
|---|---|---|
| Treasure Vault | 50% | Гарантированный tier-3 + 50 gold |
| Cursed Shrine | 30% | Tier-4 + random debuff на следующий биом |
| Vendor | 20% | 1 transmutation (3 одинаковых tier-3+ → random tier-4) |

Tap card → effect applied → continue в next biome.

---

## 7. RUN END

### 7.1 Hero death (HP = 0, no revive used)

- Run ends, return to Hub
- Soul Points calculated: `(biomes_cleared × 100) + (total_waves × 20) + (kills × 2)`
- Stats screen: biomes cleared, waves cleared, total damage, time

### 7.2 Run completion (5 биомов cleared)

- Cosmic Shards bonus
- Return to Hub

---

## 8. Edge cases

| Situation | Resolution |
|---|---|
| App close mid-combat | Save state, resume on relaunch (продолжить ту же комнату) |
| Backpack full когда враг дропает артефакт | Артефакт остаётся на полу 5 сек, despawn |
| Все 3 active slots на cooldown | Tap показывает "Cooldown" toast |
| Multi-touch / accidental input | Debounce 100ms |
| Frame drops <30fps | Combat slow-mo до 50% speed (graceful) |
| Device rotation | Locked portrait, ignore rotation events |

---

## 9. Open questions (для команды разработки)

1. Lobby duration: 45 / 60 / 90s?
2. Camera FOV в combat — 60° / 75° / 90°?
3. Hero idle animation между атаками — какой?
4. Множественные враги одновременно — сколько максимум на экране? (предлагаю 5-7)
5. Артефакт magnet pickup range — 3 tiles ОК или должно быть auto-collect от смерти врага?
6. Burst activation animation — pause game на 0.5s или real-time?
7. Boss room — открытая arena или коридор?
8. Side-step dodge — strafe в сторону или teleport-style?

---

**Next:** см. `02_BIOME_01_ICE_CASTLE.md` для детального описания первого биома (10 комнат, враги, артефакты, boss).
