# 13 — Asset Generation Prompts

**Стиль референса:** Cold War Z RPG / PunchMan / Idle Survivor типа cartoon mobile games. Чистые контурные линии, плоские цветовые блоки, дружелюбный 2D look.

**Платформа генерации:** Midjourney v6, DALL-E 3, Stable Diffusion XL, или Flux.
**Sport: portrait orientation,** background везде transparent кроме главного фона.

**Сохранять файлы в:** `/Users/deniszabirohin/2andhalfgamer/artifex_design/images/`

---

## Style anchor (использовать в каждом промпте)

```
2D cartoon mobile game art, flat illustration with thick black contour lines,
muted but vibrant colors, friendly stylized look,
similar to "Cold War Z RPG", "PunchMan", "Idle Survivor" mobile games,
clean simple shapes, no realistic shading, no photorealistic textures
```

---

# A. BACKGROUND ASSETS (биом 1: Ice Castle / зима / post-apoc city)

## Asset 1 — Sky background

**Файл:** `images/bg_sky.png`
**Размер:** 1080×1920 px
**Формат:** PNG, opaque (без прозрачности)

**Prompt:**
```
[STYLE ANCHOR]
Vertical portrait sky background for mobile game,
overcast cold winter sky, pale blue-grey gradient from white at horizon to deeper blue-grey at top,
soft diffuse clouds (no sun, no rays),
no horizon line visible (will be covered by other layers),
no buildings, no trees, no ground,
muted cool palette: #B8C5D9 (top), #DCE3ED (middle), #E8EDF3 (bottom horizon area),
empty sky composition only
--ar 9:16 --no buildings, trees, ground, sun, characters, ui
```

---

## Asset 2 — Distant city silhouette

**Файл:** `images/bg_city.png`
**Размер:** 1080×600 px
**Формат:** PNG, **transparent** background
**Композиция:** силуэт занимает нижнюю 1/3, верхняя 2/3 — пусто (transparent)

**Prompt:**
```
[STYLE ANCHOR]
Distant post-apocalyptic city silhouette, viewed from far away,
broken/ruined skyscrapers of varying heights, tilted antennas, broken radio towers,
flat dark silhouette in muted blue-grey color #6A7A8E,
slight atmospheric haze around buildings (very light fog tint),
horizontal composition, city occupies bottom third of canvas, top is transparent empty space,
buildings vary in height — tallest at center, shorter to sides,
PNG with transparent background (alpha channel),
no foreground details, no people, no vehicles
--ar 9:5 --transparent background --no sky, ground, foreground, characters, ui
```

---

## Asset 3 — Snowy path / ground (perspective)

**Файл:** `images/bg_path.png`
**Размер:** 1080×800 px
**Формат:** PNG, **transparent** top half (для seamless tile с city/sky)

**Prompt:**
```
[STYLE ANCHOR]
Snowy path leading forward with strong vanishing-point perspective,
viewed from first-person walking position,
trapezoid-shaped path narrowing toward center horizon,
light blue-white snow with subtle texture (footprint hints, light shadow patches),
path color: #E0EAEF center, #C5D4DC edges,
light cyan-blue tint near horizon (atmospheric),
NO trees, NO buildings on the path itself,
top 25% of image is transparent (sky/horizon transition area),
bottom edge is widest path edge,
clean geometric perspective, no detailed terrain
--ar 27:20 --transparent top --no trees, buildings, characters, ui, snowflakes, particles
```

---

## Asset 4 — Side tree (LEFT-side, для parallax)

**Файл:** `images/tree_left.png`
**Размер:** 400×800 px
**Формат:** PNG, **transparent** background

**Prompt:**
```
[STYLE ANCHOR]
Single tall pine tree, vertical orientation, slightly leaning to the right (this is left-side tree, foliage tilts toward center of view),
dark green needles with light snow caps on branches,
brown-grey trunk with black contour outline,
geometric stylized tree shape: triangle layers of foliage stacked vertically (3-5 layers),
foliage color: #2D5C3F shadow, #4A8761 mid, #6BAB7F highlight,
trunk color: #3E2C1F dark, #5A4030 base,
isolated on transparent background, no shadow, no ground, no other elements,
stylized cartoon mobile game asset
--ar 1:2 --transparent --no sky, ground, shadow, snow particles, ui, text, other trees
```

---

## Asset 5 — Side tree (RIGHT-side mirrored)

**Файл:** `images/tree_right.png`
**Размер:** 400×800 px
**Формат:** PNG, **transparent**

**Prompt:**
```
[same as Asset 4 but mirrored — slightly leaning to the LEFT (foliage tilts toward center), 
right-side variant for parallax]
```

---

# B. HANDS (3 states × 2 hands = 6 sprites)

Все hands — сартун рук в **жёлтой puffy winter jacket sleeve** + **brown leather glove**, тёмная кожа.

## Asset 6 — Left Hand IDLE

**Файл:** `images/hand_left_idle.png`
**Размер:** 500×700 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Left hand of dark-skinned hero in first-person view, bottom-left screen corner of mobile game,
hand visible from elbow to fist, fist clenched in idle ready pose,
yellow puffy winter jacket sleeve with stitching/quilted texture (color #E8B62E warm yellow, #C99820 shadow),
brown leather glove fits over hand (color #5C3A1F dark brown, #7C5028 mid brown),
hand and forearm angled slightly upward and to the right (visible from inside-the-hero perspective),
fist faces forward-right (knuckles direction),
thick black contour lines around silhouette (4-6 px stroke),
flat cartoon coloring, no realistic shading,
isolated on transparent background, no shadow,
mobile game UI sprite asset
--ar 5:7 --transparent --no body, head, weapon, background, shadow, ui
```

## Asset 7 — Left Hand WALK

**Файл:** `images/hand_left_walk.png`
**Размер:** 500×700 px

**Prompt:**
```
[same as Asset 6 but in walking motion pose:
hand swung slightly upward and outward (mid-stride),
arm bent at elbow ~110°, fist at chest level (higher than idle),
suggests forward locomotion]
```

## Asset 8 — Left Hand ATTACK

**Файл:** `images/hand_left_attack.png`
**Размер:** 500×700 px

**Prompt:**
```
[same as Asset 6 but in punching attack pose:
fist extended forward aggressively, arm nearly straight,
slight motion blur lines behind fist (1-2 short white speed lines),
small impact particle stars (1-2 tiny yellow stars near fist),
dynamic action frame for combat animation]
```

## Asset 9 — Right Hand IDLE

**Файл:** `images/hand_right_idle.png`
**Размер:** 500×700 px

**Prompt:**
```
[mirror of Asset 6 — right hand at bottom-right corner, knuckles facing forward-LEFT,
same yellow jacket + brown glove + dark skin,
positioned for holding weapon (slight grip pose, fingers closing as if grasping vertical rod)]
```

## Asset 10 — Right Hand WALK

**Файл:** `images/hand_right_walk.png`
**Размер:** 500×700 px

**Prompt:**
```
[mirror of Asset 7 — right hand walk pose, in ANTI-PHASE to left hand
(when left hand is up, right is down — natural walking sway)]
```

## Asset 11 — Right Hand ATTACK

**Файл:** `images/hand_right_attack.png`
**Размер:** 500×700 px

**Prompt:**
```
[mirror of Asset 8 — right hand attack pose,
fist + weapon thrust forward
(this is variant без weapon — weapon будет рендериться отдельно поверх)]
```

---

# C. WEAPONS (5 уникальных)

Все weapons — **vertical orientation**, как будто weapon торчит вверх из руки. Top of image = тип weapon, bottom = handle/grip.

## Asset 12 — Автомат (AK-47)

**Файл:** `images/weapon_ak47.png`
**Размер:** 250×500 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Cartoon AK-47 assault rifle, vertical orientation (stock at bottom, barrel pointing up),
post-apocalyptic style with worn battered appearance,
wooden stock and grip (brown #5A3E22 dark, #8B5E32 mid),
black metal barrel and receiver with scratches,
banana magazine (curved) attached at middle, dark grey,
slight yellow rust/wear marks on metal,
thick black contour outline (4-5 px),
flat cartoon coloring, no realistic photographic detail,
isolated on transparent background, no shadow,
mobile game weapon icon style
--ar 1:2 --transparent --no hand, character, background, ui, text, scope
```

## Asset 13 — Мачете (Machete)

**Файл:** `images/weapon_machete.png`
**Размер:** 200×550 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Cartoon machete weapon, vertical orientation (handle at bottom, blade pointing up),
long curved single-edged blade (steel grey #C0C5CC bright, #7A8088 shadow side),
slight rust/wear marks on blade,
brown wooden handle with leather wrap (handle ~25% of total length, color #4A3220),
small brass guard between blade and handle,
thick black contour outline,
flat cartoon style, no realistic glints,
isolated on transparent background
--ar 4:11 --transparent --no hand, character, blood, particles, ui
```

## Asset 14 — Пистолет (Pistol)

**Файл:** `images/weapon_pistol.png`
**Размер:** 200×400 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Cartoon handgun pistol (Glock-style), vertical orientation (grip at bottom, barrel pointing up),
black plastic frame and slide (color #2A2D33),
textured grip (cross-hatched pattern at handle),
slight metallic gleam highlights on slide top,
trigger guard visible,
thick black contour outline,
flat cartoon coloring, no realistic shading,
isolated on transparent background
--ar 1:2 --transparent --no hand, character, bullets, smoke, ui
```

## Asset 15 — Вантуз (Plunger)

**Файл:** `images/weapon_plunger.png`
**Размер:** 200×500 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Cartoon toilet plunger as weapon, vertical orientation (wooden handle at bottom, red rubber suction cup at top),
wooden stick handle 70% of length (color #6B4F30 with darker grain lines),
red rubber bell-shaped suction cup at top (bright red #C8302D, darker shadow side #9A2422),
slightly cartoonish with comedy weapon vibe,
thick black contour outline,
flat cartoon style with simple shading,
isolated on transparent background, no shadow, no liquid drips
--ar 2:5 --transparent --no hand, character, water, drips, ui, text
```

## Asset 16 — Дубинка (Police Baton)

**Файл:** `images/weapon_baton.png`
**Размер:** 180×500 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Cartoon police baton / nightstick, vertical orientation (grip at bottom, club end at top),
black hardened plastic/rubber material (#1F2024 dark, #3A3D43 highlight),
ribbed grip section at bottom 25%,
straight cylindrical club with slight rounded top,
small leather strap loop at handle bottom,
thick black contour outline,
flat cartoon style, simple highlights,
isolated on transparent background
--ar 9:25 --transparent --no hand, character, leather wrap details, ui, text
```

---

# D. ENEMY SPRITES (опционально — у тебя уже есть `enemy1-3.png`)

Если хочешь обновить enemy art под новый стиль:

## Asset 17 — Frost Goblin (Basic enemy)

**Файл:** `images/enemy_goblin.png`
**Размер:** 400×600 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Cartoon ice-themed goblin enemy, frontal view (facing player),
small humanoid creature, ~3 heads tall, cute-menacing aesthetic,
icy blue skin (#7BB6CD body, darker shadow areas),
torn dark fur loincloth and small ice spikes on shoulders,
holding rusty wooden club with both hands,
big yellow eyes, sharp teeth in snarling expression,
exhalation of cold breath (small white puff near mouth),
thick black contour outline,
flat cartoon coloring,
isolated on transparent background, no shadow
--ar 2:3 --transparent --no background, weapons in air, ui, multiple enemies
```

## Asset 18 — Ice Spitter (Ranged enemy)

**Файл:** `images/enemy_spitter.png`
**Размер:** 400×600 px

**Prompt:**
```
[STYLE ANCHOR]
Cartoon ice-themed ranged enemy: standing wraith-like creature,
tall humanoid with pale icy skin (#D4E5EE body, blue-tinted shadows),
dark hooded robe (deep blue #2A3E5C),
holding glowing ice orb in one hand (cyan glow #6CCFE5),
hollow pale-glowing eyes inside hood,
floating slightly above ground (small ice particles below feet),
thick black contour outline,
flat cartoon coloring, mystical aesthetic,
isolated on transparent background
--ar 2:3 --transparent --no background, ui, multiple enemies
```

## Asset 19 — Glacial Brute (Elite enemy)

**Файл:** `images/enemy_brute.png`
**Размер:** 500×700 px

**Prompt:**
```
[STYLE ANCHOR]
Cartoon massive ice golem brute, frontal view, intimidating,
huge bulky humanoid made of cracked blue ice and stone (~5 heads tall, very wide shoulders),
ice spikes protruding from back and shoulders,
glowing cyan crystalline core in chest,
heavy stone fists (rocks frozen together),
glowing white-blue eyes,
thick black contour outline,
flat cartoon coloring, menacing pose with raised fist,
isolated on transparent background
--ar 5:7 --transparent --no background, ui, smaller enemies
```

---

# E. UI / EXTRA assets (опционально, для polish)

## Asset 20 — Wooden Inventory Frame (uses в нижнем UI)

**Файл:** `images/ui_inventory_bg.png`
**Размер:** 900×500 px
**Формат:** PNG, transparent

**Prompt:**
```
[STYLE ANCHOR]
Cartoon wooden inventory panel UI element, horizontal landscape orientation,
weathered cardboard or wooden plank background (color #BDA47A base, darker grain lines),
torn edges with masking tape strips at corners (grey duct tape, X-pattern),
slight worn paper texture overlay,
empty interior (no slots drawn — slots will be added programmatically),
thick black contour outline around outer shape,
flat cartoon style,
isolated on transparent background
--ar 9:5 --transparent --no items, slots, characters, ui text, numbers
```

## Asset 21 — Day Counter Frame

**Файл:** `images/ui_day_frame.png`
**Размер:** 200×100 px

**Prompt:**
```
[STYLE ANCHOR]
Cartoon stone-button UI element, oval/circular shape,
weathered grey stone with chipped edges (color #B5B8B0),
inset border line for depth,
empty interior (text "DAY X/10" будет добавлен programmatically),
thick black contour outline,
flat cartoon style, mobile game UI button
--ar 2:1 --transparent --no text, characters, items, ui
```

---

# F. Generation tips

1. **Batch generation order** — сначала backgrounds (A), потом hands (B), потом weapons (C). Каждый block self-contained.
2. **Style consistency** — одинаковый style anchor в каждом промпте. Если используешь Midjourney — `--style raw` для cleaner cartoon look.
3. **Reference image** — если есть готовый "approved" hand sprite — можно использовать как `--cref` (character reference) в Midjourney v6 для consistency остальных.
4. **Iteration** — генерируй 4 варианта каждого, выбирай лучший. Re-prompt если ничего не подходит.
5. **Post-processing** — после generation пройдись через background remover (remove.bg или Photoshop) если transparent вышел не чистый.

---

# G. Cost estimate

- **Midjourney basic plan ($10/mo)**: ~200 generations/month → достаточно для всех 21 ассета с iterations
- **DALL-E 3 (per generation)**: ~$0.04 per image × 21 × 4 variants = $3.36 + ~10 iterations = $5
- **Stable Diffusion (local)**: free если у тебя есть GPU

---

# H. Размеры финальные (как они render'ятся в game)

| Asset | Generated size | In-game render | Reasoning |
|---|---|---|---|
| bg_sky.png | 1080×1920 | full canvas (CW × CH) | Background layer, scales to fit |
| bg_city.png | 1080×600 | CW × CH×0.4 (mid screen) | Distant atmospheric |
| bg_path.png | 1080×800 | CW × CH×0.55 (bottom half) | Tiles vertically for scroll |
| tree_left/right.png | 400×800 | scales 0.2× → 1.5× (parallax) | Spawned on horizon, scales up |
| hand_*.png | 500×700 | 240×340 (in-game) | Bottom corners overlay |
| weapon_*.png | 200-250×400-550 | 100-130×200-280 | Attached to right hand |
| enemy_*.png | 400-500×600-700 | scales по distance 0.5×-1.2× | Top-down approach |

---

# Checklist для тебя

- [ ] Generate Asset 1 (bg_sky)
- [ ] Generate Asset 2 (bg_city) — verify transparent top
- [ ] Generate Asset 3 (bg_path) — verify transparent top
- [ ] Generate Asset 4-5 (trees) — нужны 2 mirrored versions
- [ ] Generate Assets 6-11 (6 hand sprites) — 3 states × 2 hands
- [ ] Generate Assets 12-16 (5 weapons)
- [ ] Save все в `/Users/deniszabirohin/2andhalfgamer/artifex_design/images/`
- [ ] (Optional) Update existing enemy sprites (Assets 17-19)
- [ ] (Optional) UI frames (Assets 20-21)

После того как все assets сохранены — implementation по `12_FPV_VISUAL_PLAN.md`.
