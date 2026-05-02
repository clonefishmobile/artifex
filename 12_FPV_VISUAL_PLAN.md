# 12 — FPV Visual Implementation Plan

**Target:** добавить cartoon FPV визуал (как Cold War Z RPG / PunchMan) к существующему `index.html` prototype.

**Что уже есть:**
- HTML5 single-file game, Canvas 2D в `#combat-canvas`
- `drawCombat(dt)` функция (line ~521) с базовым gradient background + perspective lines
- Enemy sprites (`images/enemy1.png`, `enemy2.png`, `enemy3.png`)
- Camera shake state (`shakeAmount`, `shakeTime`)

**Что нужно добавить:**
- Layered background (sky + city + trees parallax)
- Hands в нижних углах (idle / walk / attack animations)
- Weapon overlays на hands (по active slot)
- Camera bob при walking
- Hit feedback (red flash overlay)

---

## 1. Asset подгрузка (init phase)

Добавить в начало `<script>` секции, до `resetState()`:

```js
// ===================== VISUAL ASSETS =====================
const ASSETS = {
  bgSky:     'images/bg_sky.png',         // 1080×1920
  bgCity:    'images/bg_city.png',        // 1080×600 (трансп)
  bgPath:    'images/bg_path.png',        // 1080×800 (трансп нижняя половина)
  treeLeft:  'images/tree_left.png',      // 400×800 (трансп)
  treeRight: 'images/tree_right.png',     // 400×800 (трансп)
  
  // Hands
  handLeftIdle:   'images/hand_left_idle.png',     // 500×700 трансп
  handLeftWalk:   'images/hand_left_walk.png',     // 500×700
  handLeftAttack: 'images/hand_left_attack.png',   // 500×700
  handRightIdle:  'images/hand_right_idle.png',
  handRightWalk:  'images/hand_right_walk.png',
  handRightAttack:'images/hand_right_attack.png',
  
  // Weapons (одна картинка на оружие, размещается в правой руке)
  weapon_ak47:    'images/weapon_ak47.png',     // 250×500 трансп
  weapon_machete: 'images/weapon_machete.png',  // 200×550
  weapon_pistol:  'images/weapon_pistol.png',   // 200×400
  weapon_plunger: 'images/weapon_plunger.png',  // 200×500
  weapon_baton:   'images/weapon_baton.png',    // 180×500
};

const IMG = {};
function loadAssets(cb) {
  let loaded = 0, total = Object.keys(ASSETS).length;
  for (const [key, src] of Object.entries(ASSETS)) {
    const img = new Image();
    img.onload = () => { if (++loaded === total) cb(); };
    img.onerror = () => { console.warn('missing:', src); if (++loaded === total) cb(); };
    img.src = src;
    IMG[key] = img;
  }
}
```

В `startGame()` обернуть init в `loadAssets(() => { /* current code */ })`.

---

## 2. Background state (parallax tracking)

Добавить в `resetState()`:

```js
// Visual state
bgScrollY: 0,           // global scroll position
walkSpeed: 0,           // 0 if idle, ~80 if walking
trees: [],              // active tree instances
treeSpawnTimer: 0,
handState: 'idle',      // 'idle' | 'walk' | 'attack_left' | 'attack_right'
handAttackTimer: 0,
heroBobPhase: 0,        // for breathing animation
hitFlashTimer: 0,       // for red overlay on damage
```

---

## 3. Refactor `drawCombat(dt)` — replace lines 539-552 (текущий simple background)

**Новая структура render order:**

```js
function drawCombat(dt) {
  ctx.clearRect(0, 0, CW, CH);
  
  // Update visual state
  G.bgScrollY = (G.bgScrollY + G.walkSpeed * dt) % CH;
  G.heroBobPhase += dt * 2;
  if (G.handAttackTimer > 0) G.handAttackTimer -= dt;
  if (G.hitFlashTimer > 0) G.hitFlashTimer -= dt;
  
  // Camera shake
  const sx = (Math.random()-0.5) * G.shakeAmount;
  const sy = (Math.random()-0.5) * G.shakeAmount;
  ctx.save();
  ctx.translate(sx, sy);
  
  // === LAYER 1: Sky (static, no scroll) ===
  drawLayerSky();
  
  // === LAYER 2: Distant city (very slow scroll) ===
  drawLayerCity();
  
  // === LAYER 3: Mid path (faster scroll for FPV motion) ===
  drawLayerPath();
  
  // === LAYER 4: Side trees parallax ===
  spawnTreesIfNeeded(dt);
  drawTrees(dt);
  
  // === LAYER 5: Enemies (existing rendering) ===
  // ... оставить existing enemy drawing code (lines ~580-650)
  
  // === LAYER 6: Hands + weapons (UI overlay, fixed bottom) ===
  drawHands();
  
  // === LAYER 7: Hit flash overlay ===
  if (G.hitFlashTimer > 0) {
    ctx.fillStyle = `rgba(255,0,0,${G.hitFlashTimer * 0.5})`;
    ctx.fillRect(0, 0, CW, CH);
  }
  
  // === LAYER 8: Damage numbers, projectiles (existing) ===
  // ... existing
  
  ctx.restore();
}
```

---

## 4. Layer rendering functions

```js
function drawLayerSky() {
  if (!IMG.bgSky.complete) return;
  ctx.drawImage(IMG.bgSky, 0, 0, CW, CH);
}

function drawLayerCity() {
  if (!IMG.bgCity.complete) return;
  // Distant city занимает ~30-40% screen height, верх — до горизонта (CH * 0.35)
  const cityH = CH * 0.4;
  const cityY = CH * 0.25;
  ctx.drawImage(IMG.bgCity, 0, cityY, CW, cityH);
}

function drawLayerPath() {
  if (!IMG.bgPath.complete) return;
  // Path scroll'ится в нижней половине screen (perspective)
  const pathH = CH * 0.55;
  const pathY = CH * 0.45;
  // Two copies для seamless tiling
  const offset = G.bgScrollY % pathH;
  ctx.drawImage(IMG.bgPath, 0, pathY + offset - pathH, CW, pathH);
  ctx.drawImage(IMG.bgPath, 0, pathY + offset,         CW, pathH);
}
```

---

## 5. Trees parallax system

```js
function spawnTreesIfNeeded(dt) {
  G.treeSpawnTimer -= dt;
  if (G.treeSpawnTimer <= 0 && G.walkSpeed > 0) {
    G.treeSpawnTimer = 0.8 + Math.random() * 0.6;  // every ~0.8-1.4 sec
    const side = Math.random() < 0.5 ? 'left' : 'right';
    G.trees.push({
      side,
      progress: 0,        // 0 (horizon) → 1 (passed hero)
      speedMul: 0.9 + Math.random() * 0.2,
      sizeVariant: Math.random() < 0.5 ? 0 : 1,
    });
  }
}

function drawTrees(dt) {
  const horizonY = CH * 0.45;
  const groundY = CH * 1.0;
  
  for (let i = G.trees.length - 1; i >= 0; i--) {
    const t = G.trees[i];
    t.progress += dt * 0.3 * t.speedMul * (G.walkSpeed > 0 ? 1 : 0);
    
    if (t.progress >= 1.2) {
      G.trees.splice(i, 1);
      continue;
    }
    
    // Lerp position from horizon to bottom edge + outside
    const y = lerp(horizonY, groundY + 200, t.progress);
    const scale = lerp(0.2, 1.5, t.progress);
    const offX = lerp(0.05, 0.4, t.progress);  // отдаляется от центра
    const x = t.side === 'left' 
      ? CW * (0.5 - offX) - 200 * scale
      : CW * (0.5 + offX);
    
    const img = t.side === 'left' ? IMG.treeLeft : IMG.treeRight;
    if (img.complete) {
      const w = 400 * scale;
      const h = 800 * scale;
      ctx.drawImage(img, x, y - h, w, h);
    }
  }
}

function lerp(a, b, t) { return a + (b - a) * t; }
```

---

## 6. Hands rendering

```js
function drawHands() {
  // Hand bob (idle breathing or walk swing)
  const bobY = G.handState === 'idle'
    ? Math.sin(G.heroBobPhase) * 3
    : Math.sin(G.heroBobPhase * 3) * 8;  // walk = faster bigger sway
  
  // LEFT HAND
  let leftImg = IMG.handLeftIdle;
  let leftOffsetY = bobY;
  let leftOffsetX = 0;
  
  if (G.handState === 'walk') {
    leftImg = IMG.handLeftWalk;
    leftOffsetY = Math.sin(G.heroBobPhase * 3) * 10;  // alternating
  } else if (G.handState === 'attack_left' && G.handAttackTimer > 0) {
    leftImg = IMG.handLeftAttack;
    leftOffsetY = -20;  // forward thrust
  }
  
  if (leftImg && leftImg.complete) {
    const handW = 240, handH = 340;
    ctx.drawImage(leftImg,
      -20 + leftOffsetX,                     // X (стелется за edge экрана)
      CH - handH + leftOffsetY,              // Y (от низа)
      handW, handH);
  }
  
  // RIGHT HAND (с активным оружием)
  let rightImg = IMG.handRightIdle;
  let rightOffsetY = -bobY;  // противоположная фаза для естественности
  
  if (G.handState === 'walk') {
    rightImg = IMG.handRightWalk;
    rightOffsetY = Math.sin(G.heroBobPhase * 3 + Math.PI) * 10;
  } else if (G.handState === 'attack_right' && G.handAttackTimer > 0) {
    rightImg = IMG.handRightAttack;
    rightOffsetY = -20;
  }
  
  if (rightImg && rightImg.complete) {
    const handW = 240, handH = 340;
    ctx.drawImage(rightImg,
      CW - handW + 20,
      CH - handH + rightOffsetY,
      handW, handH);
    
    // Weapon overlay (по active slot 0)
    drawWeaponInHand(0, CW - handW + 20, CH - handH + rightOffsetY, handW, handH);
  }
}

function drawWeaponInHand(slotIdx, handX, handY, handW, handH) {
  const slot = G.activeSlots[slotIdx];
  if (!slot) return;
  const weaponKey = `weapon_${slot.defId}`;
  const img = IMG[weaponKey];
  if (!img || !img.complete) return;
  
  // Position weapon относительно правой руки (примерно сверху grip area)
  const wX = handX + handW * 0.45;
  const wY = handY - handH * 0.3;  // оружие торчит вверх из руки
  const wW = handW * 0.5;
  const wH = handH * 0.85;
  
  ctx.drawImage(img, wX, wY, wW, wH);
}
```

---

## 7. State transitions (когда меняется handState)

В существующих local функциях добавить state changes:

```js
// Когда combat начинается — hero "идёт"
function enterCombat() {
  // ... existing
  G.handState = 'walk';
  G.walkSpeed = 80;  // px/sec (background scroll speed)
}

// Когда враги в melee range — hero stops
function updateCombat(dt) {
  // ... existing enemy logic
  
  const closestEnemy = findClosestEnemy();
  const isInMelee = closestEnemy && closestEnemy.dist < 1.5;
  
  if (isInMelee) {
    G.handState = 'idle';
    G.walkSpeed = 0;
  } else if (G.enemies.length > 0) {
    G.handState = 'walk';
    G.walkSpeed = 80;
  }
}

// Когда hero атакует
function heroAttack() {
  // ... existing damage calc
  G.handState = Math.random() < 0.5 ? 'attack_left' : 'attack_right';
  G.handAttackTimer = 0.3;
  setTimeout(() => {
    if (G.handAttackTimer <= 0) {
      G.handState = G.walkSpeed > 0 ? 'walk' : 'idle';
    }
  }, 300);
}

// Когда hero получает удар
function heroTakeDamage(amount) {
  // ... existing hp logic
  G.hitFlashTimer = 0.3;
  G.shakeAmount = 6;
  G.shakeTime = 0.2;
}
```

---

## 8. Active slot weapon swap

Когда игрок ставит артефакт в active slot — weapon в руке меняется автоматически (drawWeaponInHand читает `G.activeSlots[0]` каждый frame). Никакого extra кода не нужно.

---

## 9. Quality / performance

- **Tree pool cap:** max 8 одновременно. Если >8 в `G.trees` — удалить oldest.
- **Image disposal:** все assets загружены 1 раз в init, не пересоздаются.
- **Scroll modulo:** `bgScrollY % CH` чтобы не рос бесконечно.
- **Frame rate:** Canvas 2D на mobile — 60 fps достижимо при <30 sprites simultaneously.

---

## 10. Implementation order (priority)

1. **Static background** (sky + city + path) — 3 images, no scroll, just visible
2. **Static hands** в bottom corners — idle pose, no animation
3. **Weapon overlay** в right hand — статично по active slot
4. **Background scroll** when walking (just bgScrollY moving)
5. **Trees spawn + parallax**
6. **Hand bob animation** (idle breathing)
7. **Hand walk animation**
8. **Hand attack animation** (triggered by combat)
9. **Hit flash overlay**
10. **Camera shake** уже есть — verify integration

---

## 11. Edge cases

| Case | Resolution |
|---|---|
| Image not loaded yet | `if (img.complete)` check before drawImage |
| Active slot empty | Don't draw weapon overlay |
| Multiple weapons (3 slots) | Rotate в правой руке по последнему added active slot, или показывать только slot 0 |
| Lobby phase | drawCombat не вызывается, hands invisible |
| Boss room | Larger arena — может уменьшить tree spawn rate ×0.5 |

---

## 12. Open questions

1. Hand size — 240×340 px достаточно visible на 430×900 game container? Или 260×360?
2. Weapon position — torchom вверх (пистолет дулом вверх) или dулом вперёд?
3. Hand swap при attack — left/right random, или fixed (right всегда attack)?
4. Если 2-3 active slots — все weapons видны или только slot 0?
5. Tree variety — нужно сколько разных tree images? Достаточно 1 left + 1 right + variations через scale?
6. Camera bob amount — 3 px (subtle) или 5 px (more visible motion)?
