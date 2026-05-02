# 14 — Claude Code Implementation Prompt (Phase 1: Background Layers)

**Status:** Ready to ship. Скопируй prompt ниже в Claude Code и запусти.

---

## What's done

✅ Backgrounds сгенерированы и лежат в `/Users/deniszabirohin/2andhalfgamer/artifex_design/images/`:
- `bg_sky.png` (1080×1920)
- `bg_city.png` (1080×600 transparent top)
- `bg_path.png` (1080×800 transparent top)

## What's NOT done yet (skip в этой итерации)

❌ `tree_left.png`, `tree_right.png` — деревья parallax
❌ `hand_*.png` (6 sprites) — руки idle/walk/attack
❌ `weapon_*.png` (5 sprites) — AK, machete, pistol, plunger, baton

→ Эти assets добавятся в Phase 2 и Phase 3.

---

## Prompt для Claude Code (copy-paste этот блок целиком)

```
Я работаю с проектом ARTIFEX — HTML5/Canvas 2D mobile game prototype.
Главный файл: /Users/deniszabirohin/2andhalfgamer/artifex_design/index.html

ЗАДАЧА: Заменить минималистичный gradient background в `drawCombat(dt)` на layered background rendering с настоящими image assets. Это Phase 1 — только backgrounds (sky + city + path scroll). Деревья, руки, оружие будут в Phase 2/3, не трогай их сейчас.

Прочитай /Users/deniszabirohin/2andhalfgamer/artifex_design/12_FPV_VISUAL_PLAN.md секции 1-5. Это полный план. Применяй ТОЛЬКО секции которые касаются sky / city / path layers — игнорируй секции про trees (5), hands (6), weapons (8) — они для следующих фаз.

КОНКРЕТНО:

1. Добавь asset preloader перед `resetState()` в `<script>` секции:
   - Подгрузи 3 assets: bg_sky.png, bg_city.png, bg_path.png из ./images/
   - Используй паттерн `loadAssets(callback)` из 12_FPV_VISUAL_PLAN.md секции 1
   - Игнорируй failed loads (warn в console, не блокируй game)

2. В `startGame()` оберни init в `loadAssets(() => { ... existing code })`. Game должен начинаться только после загрузки assets.

3. Добавь visual state в `resetState()`:
   - `bgScrollY: 0` — текущая позиция background scroll
   - `walkSpeed: 0` — 0 если idle, ~80 если hero walks

4. Замени текущий simple background в `drawCombat(dt)` (lines примерно 539-552):
   УДАЛИ:
   - Gradient sky (createLinearGradient)
   - Perspective grid lines (for loop с moveTo/lineTo)
   - Side dark panels (rgba(20,30,50,.6) trapezoidal shapes)
   - Snowflake particles (for i<12 loop)
   
   ЗАМЕНИ НА три layered draws:
   - drawLayerSky() — bg_sky.png на весь canvas (CW × CH)
   - drawLayerCity() — bg_city.png позиционирован на mid-screen (Y = CH*0.25, height = CH*0.4)
   - drawLayerPath() — bg_path.png в нижней половине с vertical scroll (тайлится для seamless loop)
   
   Все три функции — implement по 12_FPV_VISUAL_PLAN.md секция 4.

5. Добавь scroll update в начало drawCombat():
   - `G.bgScrollY = (G.bgScrollY + G.walkSpeed * dt) % CH`
   - Сейчас G.walkSpeed = 0 всегда (статика). В Phase 2 добавим логику update'а walkSpeed.

6. ВАЖНО — НЕ ТРОГАЙ:
   - Camera shake logic (sx, sy, ctx.translate) — оставь как есть, обёрнуто вокруг наших drawLayer вызовов
   - Enemy rendering (drawImage с enemy sprites + bobbing)
   - Projectiles, effects, damage numbers
   - HUD, lobby UI, combat slots, all DOM elements
   - Game logic: combat, lobby, merge, synergies, state machine

7. После реализации:
   - Убедись что игра запускается (открой index.html в браузере)
   - В combat phase должен виден sky на фоне, city силуэт mid-screen, path внизу
   - Камеры shake при hit должна работать
   - Если bg_path должен tile-овать seamless при scroll — но walkSpeed пока 0 поэтому статичный
   - Если какой-то image не загрузился — render должен fallback к старому gradient (сделай небольшой safety check `if (img.complete && img.naturalWidth > 0)`)

8. ВЕРНИ список изменений: какие функции добавил, какие строки заменил, как тестировать.

Constraints:
- НЕ меняй game logic — только visual rendering
- Сохрани все existing функции и behavior
- Code style: vanilla JS, как в существующем файле (без frameworks, без minification)
- Preserve все existing comments / structure
- Если возникает конфликт — оставляй existing behavior, добавляй nuevo как extension
```

---

## Что Claude Code должен сделать

После выполнения prompt'а ожидаемое состояние:

1. В начале `<script>` появилась секция `// VISUAL ASSETS`:
   - Объект `ASSETS` с 3 paths
   - Объект `IMG = {}` cache
   - Function `loadAssets(cb)` с image.onload handler

2. `startGame()` обёрнут в `loadAssets(() => { ... })`.

3. В `resetState()` добавлены 2 новые fields: `bgScrollY: 0, walkSpeed: 0`.

4. В `drawCombat(dt)`:
   - Lines 539-552 (старый simple background) удалены
   - Заменены на 3 вызова: `drawLayerSky()`, `drawLayerCity()`, `drawLayerPath()`
   - Перед ними — `G.bgScrollY = (G.bgScrollY + G.walkSpeed * dt) % CH`

5. Новые функции после `drawCombat`:
   - `function drawLayerSky() { ... }`
   - `function drawLayerCity() { ... }`
   - `function drawLayerPath() { ... }`

6. Существующее не должно сломаться:
   - Title screen → Click "Начать" → Lobby phase загружается
   - Lobby → Ready → Combat phase, видны 3 layered backgrounds
   - Враги ходят/стреляют, hero автоатакует, projectiles летят
   - Hit shake работает
   - Camera shake визуально OK

---

## Тестирование (Денис, после Claude Code)

1. Открой `/Users/deniszabirohin/2andhalfgamer/artifex_design/index.html` в браузере (или `python3 -m http.server` если есть CORS issues).
2. Click "НАЧАТЬ ИГРУ".
3. Lobby phase — поставь 1-2 weapons в active slots, нажми READY.
4. Combat phase должен показать твои 3 backgrounds:
   - Sky (всё небо) на заднем плане
   - City силуэт в средней части
   - Path в нижней половине
5. Враги должны быть видны поверх backgrounds.
6. Когда hero получает damage — camera shake должен работать.

Если что-то не так — copy ошибку из browser DevTools console (F12 → Console).

---

## После успеха Phase 1

Phase 2 будет:
- Trees parallax (нужны `tree_left.png` + `tree_right.png`)
- `walkSpeed` начнёт меняться dinamically (80 при walking, 0 при melee combat)

Phase 3:
- Hands в bottom corners (нужны 6 hand sprites)
- Animations idle/walk/attack
- Weapon overlay в right hand

Phase 4:
- Hit flash overlay, polish

---

## Если что-то идёт не так

**Images не загружаются:**
- Проверь что файлы лежат в `./images/` относительно index.html
- Браузер блокирует local file:// loading? — запусти через `python3 -m http.server 8000`, открой `http://localhost:8000`

**Background выглядит криво:**
- Проверь что bg_city.png и bg_path.png имеют **transparent top** (если непрозрачный — закроют sky)
- В DevTools открой Image asset, посмотри alpha channel

**Game logic сломалась:**
- Откати через git (если используешь) или верни старый код через Claude Code follow-up
- Проверь console errors

**Performance просел:**
- Mobile preview через DevTools device toolbar — должен 60fps
- Если lag — может нужно reduce image sizes (resize bg_sky до 720×1280)
