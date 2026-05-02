# 15 — Layout Rework Plan (Hybrid Idle Format)

**Решение:** Вариант B (Hybrid) — continuous idle с always-visible inventory + FPV combat scene.

**Reference:** Cold War Z RPG / PunchMan (твой второй скриншот).

**Изменения относительно текущего state:**
- Combat не fullscreen canvas — только верхняя 50% экрана
- Inventory grid **всегда видна** в нижней части (не только в lobby)
- Hero auto-walks непрерывно, враги appears в дали на пути
- Active slots (5 шт) ряд между combat scene и inventory
- HUD top: settings + Day progress + coins/friends (как в референсе)
- Mirror background исчезает (path только внизу)
- Прицел target reticle убран

---

## Финальный layout (mobile 430×900 portrait)

```
┌────────────────────────────────────┐
│ TOP HUD (60px)                     │
│ [⚙] [Day 4/10 ▓▓▓░░] [👤0/5][🪙0] │
├────────────────────────────────────┤
│                                    │
│  COMBAT SCENE (Canvas, 50%)        │
│  ┌─ Sky                   ─┐       │
│  │  ╲ City silhouette  ╱   │       │
│  │   ╲ Path forward   ╱    │       │
│  │    ╲       👹     ╱     │       │
│  │     ╲   enemy    ╱      │       │
│  │      ╲          ╱       │       │
│  │ [🤜]            [🤛AK] │       │
│  │  hand            hand   │       │
│  └─────────────────────────┘       │
├────────────────────────────────────┤
│ ACTIVE SLOTS (90px, 5 circles)     │
│ [○] [○] [○] [○] [○]                │
├────────────────────────────────────┤
│ HP / SCORE BAR (40px)              │
│ ▓▓▓▓▓▓▓▓░░ 95/108 +9        [ℹ]   │
├────────────────────────────────────┤
│ INVENTORY GRID (260px)             │
│ ┌──┬──┬──┬──┐                      │
│ │  │  │  │  │  Drag artifacts      │
│ │  │  │  │  │  here to merge       │
│ │  │  │  │  │  Tap to add to slot  │
│ ├──┼──┼──┼──┤                      │
│ │  │  │  │  │                      │
│ │  │  │  │  │                      │
│ └──┴──┴──┴──┘                      │
└────────────────────────────────────┘
```

---

## Phases (порядок применения)

| Phase | Что | Estimated time | Зависимости |
|---|---|---|---|
| **1.5** | Quick visual fix — убрать mirror, reticle, scroll path только вниз | 20 мин Claude Code | Текущий код |
| **2** | Layout rework — combat 50% top, inventory always visible | 1 час Claude Code | После 1.5 |
| **3** | HUD redesign — Day progress bar, settings, coins | 30 мин | После 2 |
| **4** | Hands + weapons render | 30 мин | Hands assets generated |
| **5** | Enemy spawn + walking behavior tweaks | 45 мин | После 4 |

---

# PHASE 1.5 — Quick Visual Fix

**Цель:** Убрать визуальные баги (mirror background, прицел) без архитектурных изменений.

## Claude Code prompt:

```
Я работаю с /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Quick visual fix combat phase. Убрать визуальные баги без изменения game logic.

КОНКРЕТНО:

1. В функции drawLayerPath() — убедись что path рендерится ТОЛЬКО в нижней половине экрана (от Y = CH*0.45 до CH). Если сейчас он рендерится в верхней половине тоже (mirror) — это баг tiling. Возможные причины:
   - Двойной drawImage с offset (для seamless scroll loop) рисует tile в неправильном месте
   - bg_path.png файл сам имеет mirror в текстуре (нужно проверить)
   
   FIX: должен быть только один path tile в нижней половине + второй tile ниже screen для подготовки к scroll. Никогда не рисовать path выше Y = CH*0.45.

2. Top половина экрана (от 0 до CH*0.45) должна показывать:
   - bg_sky (вся область)
   - bg_city силуэт mid-screen (Y=CH*0.25, height=CH*0.2)
   - НЕ должно быть никакого path там

3. В drawCombat() удали "target reticle" над врагами — это пунктирный круг с прицелом который рендерится в текущей версии. Найди код который рисует красный пунктирный круг (вероятно ctx.setLineDash + arc) и удали.

4. Удали или сделай subtle подсказку "← A/D свайп уклонение · Автобой →" из combat-ui dodge-hint. Сейчас она занимает много места в нижней части. Сделай opacity 0.3 + меньше размер, или убери совсем.

5. Враги должны рендериться в ОБЛАСТИ PATH (нижняя половина, между Y=CH*0.45 и CH*0.85). Не в верхней половине, не закрывая sky/city.

6. Wave counter "Room 1/10" в top right — убедись что не overlapping с HP bar. Если перекрывается — переместить на новую строку под HP bar.

ВАЖНО — НЕ ТРОГАЙ:
- Game logic, lobby phase, merge, synergies, active slots
- Inventory rendering в lobby
- Enemy AI, projectiles
- HP bar functionality

Просто визуальный cleanup. После реализации скриншот не должен иметь:
- mirror фон
- прицел над врагами
- путь в верхней половине
- большую подсказку про свайп

Открой index.html в браузере после правок — combat scene должен показать чёткий FPV: sky сверху, city силуэт, path только внизу, враги стоят на path, никаких прицелов.

Верни список измененных строк (line numbers) и краткое описание каждого fix.
```

---

# PHASE 2 — Layout Rework (Hybrid Format)

**Цель:** Превратить game в continuous idle — inventory всегда снизу, combat scene компактно сверху.

**ВАЖНО:** Это значимый rewrite. Делай ПОСЛЕ Phase 1.5.

## Claude Code prompt:

```
Я работаю с /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Layout rework — превратить fullscreen combat в верхнюю половину экрана. Inventory grid должна быть видна ВСЕГДА (continuous idle play, как в Cold War Z RPG / PunchMan).

ТЕКУЩАЯ АРХИТЕКТУРА:
- Lobby phase = fullscreen DOM (#lobby) с grid + active slots
- Combat phase = fullscreen Canvas (#combat-canvas) с врагами
- Игрок переключается между ними через Skip / Ready buttons

НОВАЯ АРХИТЕКТУРА (Hybrid):
- Top HUD (60px) — Day counter, settings, coins
- Combat scene (Canvas, 50% screen height) — sky + path + враги + hands
- Active slots row (90px) — 5 circles ряд
- HP bar (40px)
- Inventory grid (260px) — ВСЕГДА видна
- Lobby phase убирается. Merge можно делать во время combat (drag-drop работает в любой момент).

КОНКРЕТНО:

1. В CSS обнови размеры:
   - #game: оставь 430x900 max
   - #combat-canvas: position absolute, top:60px (под HUD), height 50% of game container, width 100%
   - #combat-ui (active slots): уменьши до 90px height, position absolute, top: 60px + 50% of game height
   - Создай новый div #persistent-bottom: position absolute, bottom 0, height 300px, contains HP bar + inventory grid

2. Перемести inventory grid из #lobby в #persistent-bottom:
   - Move #grid-container и #queue-container в #persistent-bottom
   - В #persistent-bottom: queue сверху, grid под ним, всегда visible
   - HP bar (existing #hud .hp-container) переместить в #persistent-bottom над grid

3. УБЕРИ lobby phase split:
   - Удали Lobby/Combat phase variable из state
   - Удали #lobby-actions кнопки (Skip, Ready, Rotate) — игра continuous
   - Auto-start combat сразу после loadAssets()
   - Lobby phase logic превращается в "background continuous merge" во время combat

4. Combat update loop:
   - Hero auto-walks вперёд (G.walkSpeed = 80)
   - Враги spawn'ятся каждые ~5-8 sec в дали (top of canvas, perspective tiny size)
   - Approaching enemies scale up по distance
   - Когда enemy в melee range (Y ≈ canvas height) — hero stops, attacks, enemy dies, hero resumes walking
   - Drops автоматически в queue (artifact icons падают в #queue-container)
   - Игрок drag'ает из queue в grid в real-time во время combat

5. Active slots row (5 circles) между canvas и inventory:
   - 5 круглых slots, 60px diameter каждый, gap 8px
   - Tap по active slot во время combat = trigger ability (как сейчас)
   - Empty slots видны как пустые круги (placeholder)
   - Active slot 4 и 5 disabled до Hero Board D7 / D14

6. HP bar над inventory:
   - Простая горизонтальная полоса, gradient red-yellow-green
   - Текст "95/108 +9" в центре
   - Width 90% of bottom area, centered

7. Combat phase больше не "ends" — continuous play. Wave clear когда все враги убиты, после короткой паузы (1.5 sec) spawn next wave. После 10 waves → carry-over screen (сохраняй existing logic для этого).

8. УБЕРИ:
   - Title screen "ARTIFEX BACKPACK ROGUELIKE" → can keep, но click → loadAssets → straight into game (skip lobby)
   - Skip / Ready / Rotate buttons из UI

9. CSS обнови:
   - #lobby — display: none permanently или удали элемент
   - Анимации lobby phase enter/exit удалить
   - #combat-ui visible всегда (active slots row)

ВАЖНО:
- Сохрани merge logic (drag-drop), synergy triggers, weapon ability cooldowns
- Сохрани carry-over screen после wave 10
- Game должна быть playable end-to-end после rework
- Mobile-first responsive: всё должно fit в 430×900

После реализации:
- Открой в браузере
- Сразу должна быть видна combat scene наверху + inventory grid снизу одновременно
- Hero должен auto-walk, враги spawn'иться в дали, бой автоматический
- Игрок может drag-drop в grid в любой момент во время combat
- Tap на active slot = ability fires

Верни список изменений (CSS, JS) с line ranges + описание архитектурного изменения.
```

---

# PHASE 3 — HUD Redesign

**Цель:** Top bar match референс — settings + Day progress bar + 0/5 friends + coins.

## Claude Code prompt:

```
Я работаю с /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Redesign top HUD под референс (Cold War Z RPG style).

ТЕКУЩИЙ HUD (line ~36-49):
- room-info ("Ice Castle 1/10")
- HP bar
- timer

НОВЫЙ HUD layout (от left to right):
1. Settings gear icon (40×40 px) — ⚙ button, opens settings modal
2. Day counter capsule (90×40 px) — "DAY 4/10" с golden border
3. Progress bar с биом checkpoints (200×40 px) — green progress bar with 4 icons:
   - 📍 (start point, green)
   - 📦 (treasure chest checkpoint, mid-biome, gold)
   - ⚔ (combat checkpoint, mid-biome)
   - ⚔ (final boss checkpoint)
4. Friends counter capsule (60×40 px) — "0/5 👤" stone-style border
5. Coins capsule (50×40 px) — "0 🪙" gold coin icon

CSS:
- #hud height 60px (was 46)
- Все элементы flex с gap 8px
- Background: linear-gradient(180deg, rgba(0,0,0,0.6), transparent)
- Padding: 8px 12px

Удали из HUD:
- room-info text (remove or move into combat scene as overlay)
- Old hp-container (HP теперь в bottom area, см Phase 2)
- Old timer

HTML structure:
```html
<div id="hud">
  <button id="settings-btn">⚙</button>
  <div class="day-counter">DAY 4/10</div>
  <div class="progress-bar">
    <div class="progress-fill"></div>
    <div class="checkpoint" data-type="start">📍</div>
    <div class="checkpoint" data-type="chest">📦</div>
    <div class="checkpoint" data-type="combat">⚔</div>
    <div class="checkpoint" data-type="boss">⚔</div>
  </div>
  <div class="friends-counter">0/5 👤</div>
  <div class="coins-counter">0 🪙</div>
</div>
```

CSS styling:
- Каждая capsule: rounded grey-gradient background с border, like stone button
- Day counter: golden text on dark grey
- Progress bar: green fill (gradient #4CAF50 → #66BB6A), transitions smooth
- Checkpoints positioned absolutely along progress bar (start = 0%, chest = 33%, combat = 66%, boss = 100%)
- Active checkpoint (где сейчас игрок) — golden glow

JS state:
- Add G.day = 4 (current day из биома)
- Add G.maxDay = 10 (биом length)
- Add G.coins = 0
- Add G.friends = 0
- Add G.maxFriends = 5
- updateHUD() function — called каждый wave clear

Update progress bar logic:
- progress = (G.room - 1) / 10  (current room в биоме)
- Highlight passed checkpoints in green/gold

ВАЖНО:
- Сохрани existing HP bar (теперь в bottom area)
- Settings button пока inactive (placeholder onClick)
- HUD должен быть above all other UI z-index 50

После реализации проверь visual: top bar должен показывать settings (left) + day capsule + progress + friends + coins (right). Похоже на референс скриншот.
```

---

# PHASE 4 — Hands + Weapons

**Цель:** Render hands в bottom of combat canvas + weapon в right hand.

**Зависимость:** Sprites должны быть готовы (см. `13_ASSET_GENERATION_PROMPTS.md` секции B и C).

## Claude Code prompt:

```
Я работаю с /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Render hands в combat canvas + weapon overlay в правой руке.

ASSETS которые должны быть в /images/:
- hand_left_idle.png, hand_left_walk.png, hand_left_attack.png (500×700 каждая)
- hand_right_idle.png, hand_right_walk.png, hand_right_attack.png
- weapon_ak47.png, weapon_machete.png, weapon_pistol.png, weapon_plunger.png, weapon_baton.png

КОНКРЕТНО:

1. Расширь loadAssets():
   - Добавь все 6 hand sprites + 5 weapon sprites
   - Если файл missing — fallback к null, не crash

2. Добавь visual state в G:
   - handState: 'idle' | 'walk' | 'attack_left' | 'attack_right'
   - handAttackTimer: 0
   - handBobPhase: 0

3. Add updateHandState(dt):
   - Если G.walkSpeed > 0 → 'walk'
   - Если враг в melee range → 'idle'
   - Если только что атаковали (handAttackTimer > 0) → 'attack_left' or 'attack_right'

4. Add drawHands():
   - Render в нижней части canvas (bottom corners)
   - Left hand: position (-30, CH*0.95 - handH*0.7), size 240×340 (часть скрыта за edge)
   - Right hand: position (CW - 210, CH*0.95 - handH*0.7), size 240×340
   - Bob animation: idle = sin(phase) * 3px, walk = sin(phase * 3) * 8px
   - Attack pose = forward thrust -20px Y, swap to attack sprite for 0.3 sec

5. Add drawWeaponInHand(slotIdx):
   - Read G.activeSlots[0] (use slot 0 weapon as right hand visible)
   - If slot empty — no weapon
   - Render weapon image над right hand: position (handX + handW*0.4, handY - handH*0.3)
   - Weapon scale: handH * 0.85 vertical
   - Slight tilt rotation (15°) для natural grip look

6. В drawCombat() add calls:
   - После drawTrees() (или enemies) и перед effects/projectiles:
     drawHands();
     drawWeaponInHand(0);
   - Они должны быть POVERH врагов (UI overlay layer)

7. Trigger animations:
   - В heroAttack() existing function: set G.handAttackTimer = 0.3, set G.handState = random('attack_left' or 'attack_right')
   - В update() add: if G.handAttackTimer > 0: G.handAttackTimer -= dt; иначе G.handState вернётся к 'walk' или 'idle'

8. Edge cases:
   - Image не загружен (img.complete === false) → пропустить рендер той руки
   - Active slot пустой → не рендерить weapon
   - Если в slot 0 нет weapon но есть в slot 1 — используй slot 1 для visible weapon

ВАЖНО:
- Hands рендерятся ВНУТРИ canvas, не как DOM элементы
- Они visible only во время combat (canvas active)
- Размеры пропорциональны canvas (не absolute pixels)

После реализации:
- В combat scene должны быть видны 2 руки в нижних углах
- Если активный slot 0 = AK-47 — его sprite видим в правой руке
- Hands "дышат" (idle bob) когда hero stops
- Hands swing при walking
- Hands punch forward когда атака

Верни список added функций + описание интеграции.
```

---

# PHASE 5 — Enemy & Combat Behavior

**Цель:** Враги appear в дали → приближаются → бой → drop. Match референс flow.

## Claude Code prompt:

```
Я работаю с /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Update enemy spawn + hero walking behavior. Continuous flow в стиле Cold War Z / PunchMan / Survivor.io.

ТЕКУЩЕЕ ПОВЕДЕНИЕ:
- Враги spawn'ятся в начале wave, стоят на месте, hero идёт к ним
- После kill всех — wave clear

НОВОЕ ПОВЕДЕНИЕ:
- Враги auto-spawn'ятся в дали (top of path, near horizon)
- Они scale up + move toward hero (faux-3D approach)
- Hero auto-walks forward (path scrolls), но stops если enemy в melee range
- После kill enemy — hero resume walks → next враги spawn → loop

КОНКРЕТНО:

1. Enemy state:
   - distanceFromHero: float 0-1 (1 = at horizon, 0 = at hero)
   - approachSpeed: 0.05-0.15 per second (различная для типов)

2. Spawn logic:
   - Каждые 4-7 sec spawn новый enemy если currentEnemies.length < 5
   - Spawn position: distanceFromHero = 1.0 (horizon)
   - Type weighted random per биом (60% basic, 25% ranged, 15% elite в Ice Castle)

3. Update в drawCombat() / updateCombat():
   - Каждый frame: enemy.distanceFromHero -= enemy.approachSpeed * dt
   - Enemy reach distanceFromHero <= 0.1 → in melee range, hero stops
   - Render enemy в perspective:
     - Y position = lerp(canvas.horizonY, canvas.heroY, 1 - enemy.distanceFromHero)
     - Scale = lerp(0.2, 1.5, 1 - enemy.distanceFromHero)
     - X position = lerp(centerX_horizon, enemy.targetX_groundLevel, 1 - enemy.distanceFromHero)
   - Multiple enemies stagger по X для visual clarity

4. Walk/stop logic:
   - Если есть enemy with distanceFromHero <= 0.15: G.walkSpeed = 0, hero stops
   - Иначе G.walkSpeed = 80, path scrolls
   - Hands switch idle/walk соответственно

5. Hero attack:
   - Когда враг в melee range — auto-attack каждую 1.0 sec
   - Damage applied, враг получает hit, HP уменьшается
   - На 0 HP — enemy dies, drop artifact, removed
   - После remove — hero resumes walking

6. Side-step dodge при ranged enemy projectile:
   - Ranged enemy shoots projectile (visual: small ice ball, travels к hero)
   - Player swipe LEFT/RIGHT — hero side-steps 1 tile, projectile misses
   - Cooldown 0.3 sec

7. Wave / Room counter:
   - Wave clear = X enemies killed (10 для room 1, 12 для room 2, ..., 30+ для boss)
   - Каждый room имеет fixed enemy count (existing data)
   - После всех killed — short pause (1.5 sec) → next room transition (bg_path color shift?)
   - Day counter в HUD updates: G.day += 1 если room cleared

8. Visual polish:
   - Enemy spawn animation: появляются с puff of smoke/snow
   - Approach: subtle bobbing motion + scale lerp
   - Death: shrink + fade (0.5 sec) + artifact drop animation

9. Side-step dodge удалить как input если game обходится без — но keep как backup if Денис хочет use later

После реализации:
- Combat должен flow continuously
- Враги appears в дали (мелкие), приближаются, hero stops, бой, kills, walk resume
- Path scroll'ится при walking, статичен при combat stop

Верни описание изменений в game loop + примеры численных параметров для balance.
```

---

# Order of execution

**Recommended sequence:**

1. **СЕЙЧАС:** Скопируй Phase 1.5 prompt → Claude Code → applies → проверь screenshot → ✅
2. **Затем:** Phase 2 (architecture) — самая большая, делать когда есть час и focus
3. **После 2:** Phase 3 (HUD) — quick win, visual cleanup
4. **Параллельно:** Сгенери hands + weapons sprites (см. `13_ASSET_GENERATION_PROMPTS.md`)
5. **Когда есть assets:** Phase 4 — render hands
6. **Финал:** Phase 5 — enemy behavior tweaks

**Total estimated time:** 3-4 часа Claude Code + 1-2 часа image generation = 5-6 часов чтобы получить full referenced layout + behavior.

---

# Что я предлагаю прямо сейчас

**Скопируй Phase 1.5 prompt в Claude Code сейчас** — это 20-минутная правка визуальных багов без архитектурных изменений. Скриншот после Phase 1.5 уже должен быть значительно ближе к референсу:
- Чистый sky сверху + city силуэт + path внизу (без mirror)
- Враги стоят на path в нижней части
- Никаких прицелов / пунктирных кругов
- Inventory остаётся в lobby phase как сейчас (Phase 2 это поменяет)

После Phase 1.5 — пришли скриншот, посмотрим что осталось. Затем Phase 2.
