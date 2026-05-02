# 05 — Combat System

## 1. Camera & View

### 1.1 First-Person perspective
- FOV: 75° default (60° alternate for performance, 90° for mobile wide-screen testing)
- Near clip: 0.1 units, far clip: 50 units
- Camera position: ~1.8 units height (eye level), centered horizontally in corridor
- **Camera bob**: subtle 2–3 px vertical oscillation during auto-walk (0.5 Hz sine wave, dampens on stop)
- Hand/weapon visibility: lower 1/3 screen, always visible during combat, slight swing animation synchronized with walk cycle

### 1.2 HUD Layout
- **HP bar**: bottom-left corner, 120 px wide × 16 px tall, gradient fill (red → yellow → green), label "HP: X/Y" in pixel font
- **Active burst slots**: 3 × 100 px icons centered at bottom, 20 px gap between, cooldown arc overlay (white stroke 4 px)
- **Wave counter / mini-map**: top-right corner, "Wave X/10" text + small radar (80 × 80 px, white dots for enemies, blue dot for hero)
- **Status effects**: top-left, stacked icons (shield, poison drone, berserk aura) with remaining duration numbers

---

## 2. Hero Mechanics

### 2.1 Movement

**Auto-walk behavior:**
- Forward speed: 2.0 tiles/sec (1 tile = 80 px world-space units)
- Pathfinding: A* algorithm, corridor-only (no backtracking in MVP)
- Obstacle map: generated per-room (walls, doors, enemy positions)
- Movement stops when:
  - Enemy in melee range (≤1.0 tile distance in front)
  - Door blocked (hero pauses, waits for room load)
  - All enemies dead in current room
  - Reached end-of-corridor / room transition zone

**Edge case handling:**
- Hero at corridor edge + swipe dodge: no horizontal displacement, animation plays (feedback only)
- Stuck detection: if A* fails, hero retries pathfind every 0.5 sec; after 3 sec stuck, teleport to nearest valid floor tile
- Multiple obstacles: path recalculates every frame (50 ms update cycle)

### 2.2 Auto-Attack

**Attack stats:**
- Base damage formula: `base_dmg = 10 + (hero_level × 2)`
- Attack speed: 1.0 attack/sec (1.0 sec cooldown per strike) — modifiable through synergy buffs
- Attack range: melee, 1.0 tile in front of hero (no range extension in MVP)
- Crit chance: 15% baseline, +N% from red school synergies
- Crit multiplier: 2.0× (200%) — modifiable through artifact perks
- Damage application: instant hit-scan (no travel time)

**Target selection:**
- Primary: nearest enemy by Euclidean distance (recalculate every 0.3 sec)
- Override: player tap on enemy (see 3.2)
- Reset: if target dies → auto-select next nearest
- Multiple same-distance: tie-break by spawn order (FIFO)

**Attack animation:**
- Wind-up: 0.3 sec (visual feedback of upcoming hit)
- Impact frame: at 0.3 sec (damage applies to enemy)
- Recovery: 0.2 sec (total 0.5 sec per attack cycle)
- Hero hand/weapon swing: synchronized with impact frame

### 2.3 Hero HP & Death

**HP pool:**
- Max HP: `max_hp = 100 + (hero_level × 10) + artifact_bonuses`
- Regen: 0 baseline (only through green school synergies, e.g., +2 HP/sec passive)
- Shield mechanic: Barrier Field burst applies temporary +40% max HP absorb layer

**HP UI:**
- Bar gradient: red (0–33%), yellow (33–66%), green (66–100%)
- Damage indicator: brief white flash on bar when hit
- Healing indicator: green number +X float upward from bar

**Death state:**
- Trigger: HP ≤ 0
- Screen effect: 0.5 sec red vignette fade to black
- State change: transitions to fail UI (retry button, summary screen)
- No respawn in combat (run ends, return to lobby)

---

## 3. Player Inputs

### 3.1 Swipe LEFT / RIGHT — Side-Step Dodge

**Input recognition:**
- Detection threshold: ≥40 px horizontal distance, max 200 ms stroke duration
- Horizontal velocity: ≥100 px/sec (prevents accidental swipes)
- Vertical tolerance: ±50 px vertical deviation allowed (loose Y constraint)
- Touch point: anywhere on screen (including over HUD elements)

**Dodge mechanics:**
- Displacement: 1.0 tile perpendicular to forward direction (left or right)
- Animation duration: 0.2 sec smooth easing (easeInOutQuad)
- Dodge cooldown: 0.3 sec between consecutive dodges (player can spam, but not fast)
- Forgiveness window: ±0.5 tile hit-detection radius (player can dodge slightly past projectile)
- Input buffer: 33 ms tolerance for dodge timing (allows 2 frames latency absorption)

**Edge cases:**
- Hero at corridor edge (wall within 1 tile): dodge blocked, no displacement, animation plays (visual feedback only)
- Multi-touch: primary touch wins (lowest touch ID), secondary touches debounced 100 ms
- Swipe over UI elements (slot buttons): UI gets priority, no dodge triggered
- Swipe < 40 px (fling fail): fallback tap-to-position activates (0.2 sec delay for intent detection)

**Visual feedback:**
- Dodge initiated: hero body tilt 5° toward dodge direction, 0.1 sec
- Dodge in-progress: motion blur effect (subtle, 2–3 trail frames at 0.5 opacity)
- Dodge complete: snap back to neutral stance

### 3.2 Tap on Enemy — Focus Fire

**Input detection:**
- Hitbox: 60 × 60 px screen-space collision box centered on enemy sprite
- Tap tolerance: 25 px tap radius (center ±25 px allows imprecise taps)
- Cooldown on switch: 0.2 sec (prevent rapid re-targeting spam)

**Visual feedback:**
- Selected enemy: red crosshair overlay, 0.3 sec scale-up animation (100% → 120% → 100%)
- Crosshair color: red (140, 20, 20) with 200° rotating ring animation
- Unselected enemies: crosshair fades out

**Behavior:**
- Effect: hero changes auto-attack target to tapped enemy immediately
- Persistence: target remains until death or 8 sec timeout (auto-reverts to nearest)
- Reset on death: if selected target dies → auto-selects next nearest enemy
- Pre-selection: if hero already attacking target → tap confirms (visual feedback strengthens)

**Edge case:**
- Tap on overlapping enemies: top of rendering stack wins (highest z-order)
- Tap on dead enemy: no-op, tap consumed (0 feedback)
- Rapid re-tap same enemy: ignored (no target change, 0.2 sec cooldown)

### 3.3 Tap on Active Burst Slot — Burst Activation

**Input detection:**
- Hitbox: 100 × 100 px per slot icon (centered on visual element)
- Tap tolerance: 30 px tap radius

**Cooldown check:**
- If cooldown > 0: show "Cooldown" toast overlay (center-bottom, 0.5 sec fade-in/out, no block)
- If cooldown = 0: proceed to activation

**Activation sequence:**
1. Instant trigger (no delay)
2. Apply cooldown timer to slot (see burst type specs for duration)
3. Play burst activation animation (0.3 sec screen tint color matching school)
4. Trigger burst effect on nearest N enemies or entire screen (see 4.x)

**Visual feedback:**
- Slot icon flash: 0.2 sec white glow, then fade to normal
- Screen tint: brief colored overlay (red for red school, blue for blue, green for green) at 20% opacity, 0.2 sec fade
- Damage numbers: floating damage text on hit enemies (+damage integer, fades upward 0.8 sec)
- Particle effects: school-specific VFX burst (see 4.x)

**Edge cases:**
- Tap during burst animation: queued, activates after current burst completes (0.3 sec max queue)
- All 3 slots on cooldown: tap shows "Cooldown" toast, no error sound
- Tap after wave ends: burst plays on empty field (no enemies to damage, visual only)
- Tap while hero dead: no-op (combat over)

---

## 4. Burst Types (5 Base Abilities)

### 4.1 Missile Strike (Red School Slot)

**Mechanics:**
- Trigger: instant on tap
- Effect: 3 homing projectiles spawn from hero, fly to nearest 3 enemies, each deals `2.5 × hero_damage`
- Range: full screen (no distance cap)
- Targeting: auto-select nearest 3 unique enemies (if <3 alive, hit all)
- Projectile speed: 5 tiles/sec
- Travel time: typically 0.4–0.8 sec depending on distance
- Cooldown: 8 sec

**Animation & VFX:**
- Projectile model: small red glowing sphere, 0.3 sec tail trail
- Impact: small explosion sprite (48 × 48 px), 0.3 sec duration
- Sound: "pew" SFX × 3 (staggered 66 ms apart)
- Screen shake: light (4 px) at impact, 0.2 sec

**Damage application:**
- Applies on projectile collision (hit-scan to enemy center)
- Crits apply (roll each projectile independently)
- Overkill damage: excess damage lost (no splash)

### 4.2 Barrier Field (Blue School Slot)

**Mechanics:**
- Trigger: instant on tap
- Effect: hero gains temporary shield equal to +40% of max HP, lasts 5 sec
- Stacking: shield refreshes (does not stack multiple casts), resets duration to 5 sec
- Damage absorption: shield takes damage before hero HP
- Cooldown: 12 sec

**Animation & VFX:**
- Shield appearance: blue translucent sphere around hero, 0.5 sec fade-in
- Pulsing ring: concentric rings expand outward every 0.5 sec (subtle animation)
- Damage taken: brief shield flicker white on impact
- Shield break: shield pops (particle burst) if depleted mid-duration
- Sound: "shield_on" SFX on activation, "shield_pop" SFX on break
- Screen shake: none (defensive ability)

**UI indicators:**
- Shield bar: separate blue bar above HP bar, displays absorb value
- Duration timer: shows remaining seconds (5 → 4 → ... → 0)

### 4.3 Plague Swarm (Green School Slot)

**Mechanics:**
- Trigger: instant on tap
- Effect: 3 poison drones spawn around hero, orbit at 1 tile radius, attack for 8 sec duration
- Drone behavior: each targets nearest enemy within 3 tile radius, auto-attacks
- Drone damage: `0.5 × hero_damage` per hit, 1 hit/sec per drone
- Total DPS: ~1.5 × hero_damage sustained (3 drones at 0.5 dmg each)
- Stacking: max 6 drones active (if re-cast, oldest 3 drones despawn)
- Cooldown: 15 sec

**Animation & VFX:**
- Drone model: small green orb, 0.3 sec orbit animation around hero
- Drone attack: brief green projectile from drone → enemy center (0.3 sec travel)
- Poison effect: green glow on damaged enemy, 1 sec duration
- Sound: "buzz" ambient loop (quiet, 0.3 sec cross-fade), individual "sting" SFX per hit
- Despawn: green dissolve animation, 0.3 sec fade

**UI indicators:**
- Drone counter: small overlay "3 drones active" near burst slot, fades when duration < 2 sec
- Duration bar: semi-transparent green bar tracking 8 sec countdown

### 4.4 Berserk (Red+Green Hybrid Slot)

**Mechanics:**
- Trigger: instant on tap
- Effect: hero damage output +80% for 3 sec, costs 10% of current HP (non-lethal)
- Damage buff: multiplicative (stacks with other buffs from synergies)
- Cost: damage taken from the ability is instant (red VFX flash)
- Stacking: re-cast refreshes duration to 3 sec, applies cost again
- Cooldown: 20 sec

**Animation & VFX:**
- Aura effect: red+green swirl around hero, 0.5 sec fade-in
- Hero hands glow: red tint on hands/weapon, 1.5 brightness
- Screen tint: brief red overlay (10% opacity), 0.2 sec fade
- Sound: "berserk_activate" SFX (aggressive growl)
- Particle burst: small red sparks around hero, 10 particles, 0.5 sec duration
- Screen shake: medium (6 px), 0.3 sec

**UI indicators:**
- Berserk timer: "BERSERK 3s" label near HP bar, bold red font, fades as duration expires
- Damage multiplier: "+80%" indicator flashes on burst slot during active duration

### 4.5 Time Warp (Blue+Any Hybrid Slot)

**Mechanics:**
- Trigger: instant on tap
- Effect: all enemies on screen freeze (no movement, no attacks) for 2 sec
- Freeze scope: every enemy currently alive (including projectiles mid-flight)
- Projectile handling: in-flight projectiles freeze at current position, resume on unfreeze
- Enemy AI: pause state machine completely (animations freeze mid-frame)
- Stacking: re-cast extends freeze by 2 sec (max 4 sec total freeze per ability)
- Cooldown: 25 sec

**Animation & VFX:**
- Screen effect: blue-tinted overlay (20% opacity), 0.3 sec fade-in, 0.2 sec fade-out
- Time distortion: slight radial blur effect (subtle, 2–3 px radius blur)
- Enemy models: blue crystalline outline, frozen in mid-animation
- Projectiles: blue glow, frozen trails visible
- Sound: "time_warp_activate" SFX (deep harmonic tone), "time_warp_deactivate" SFX (reverse tone)
- Screen shake: none (temporal effect)
- Particle effect: blue sparkles around screen edges, falling slowly downward during freeze

**UI indicators:**
- Freeze timer: "FROZEN 2.0s" label center-top, bold blue font, updates every 0.1 sec
- Enemy silhouettes: brief blue flash when freeze begins

---

## 5. Enemy AI Behaviors

### 5.1 Basic Melee Enemy

**Attributes:**
- HP: `base_hp = 20 + (room_difficulty × 3)`
- Speed: 1.5 tiles/sec (slower than hero 2.0 tiles/sec)
- Attack range: melee, 1.0 tile contact
- Attack speed: 1 hit/2 sec (0.5 attacks/sec)
- Damage: `melee_dmg = 5 + (room_difficulty × 1)`

**AI logic:**
1. Detect hero within 5 tile sight range
2. Pathfind toward hero using A* (same obstacle map as hero)
3. If within attack range: attack (swing animation, 0.4 sec wind-up + 0.1 sec hit)
4. If no hero in sight: idle (play idle animation loop)
5. On death: 0.5 sec dissolve animation + artifact drop

**Behavior states:**
- Idle: wander slowly in room, no aggression
- Alert: detected hero, pathfind + chase
- Attacking: melee swing, hit on frame 15 (0.25 sec into 0.4 sec wind-up)

**Player counter:** side-step dodge (melee range avoidance)

### 5.2 Ranged Enemy

**Attributes:**
- HP: `base_hp = 15 + (room_difficulty × 2)`
- Speed: 0 (stationary at spawn)
- Attack range: full screen (projectile)
- Attack speed: 1 shot/3 sec (0.33 attacks/sec)
- Damage: `ranged_dmg = 8 + (room_difficulty × 1)`
- Projectile speed: 3 tiles/sec

**AI logic:**
1. Spawn at fixed location in room
2. Face hero (turn animation, 0.2 sec)
3. Every 3 sec: fire projectile toward hero predicted position (lead shot by 0.3 sec)
4. If hero melee (≤1.5 tiles): retreat 2 tiles away, resume shooting
5. On death: 0.5 sec dissolve + artifact drop

**Projectile behavior:**
- Model: small red/green sphere (colored by enemy type)
- Collision: hit-scan to hero center on contact
- Damage: full projectile damage to hero HP
- Speed: constant 3 tiles/sec (no acceleration)
- Travel time: ~0.5 sec from spawn to hero (40 px distance at 1280 px screen)

**Player counter:** side-step dodge (dodge perpendicular to projectile direction)

**Edge case:** if ranged enemy pathfind out of bounds, clamp position to room bounds

### 5.3 Elite Enemy (Melee with AOE)

**Attributes:**
- HP: `base_hp = 40 + (room_difficulty × 5)`
- Speed: 1.2 tiles/sec (slower than basic)
- Attack range: melee + AOE
- Attack speed: 1 hit/3 sec + AOE telegraph 2 sec
- Damage: melee `1.5 × basic_dmg`, AOE `2.0 × basic_dmg`

**AI logic:**
1. Pathfind toward hero (same as basic melee)
2. Every 3 sec: trigger AOE attack sequence:
   - Telegraph phase (2 sec): red circle appears on floor, 2 tile radius, pulsing animation
   - Detonation phase (instant): deal damage to all enemies within circle radius
3. During telegraph: player can dodge out of radius (main mechanic)

**AOE specifications:**
- Radius: 2.0 tiles (160 px world-space)
- Damage per enemy: `2.0 × basic_enemy_damage`
- Damage application: instant hit-scan all targets in radius
- Crits: AOE attacks do not crit (fixed damage)

**Visual telegraph:**
- Circle model: red semi-transparent ring, pulsing 0.5 sec cycle
- Center label: "!" danger icon, animated scale 0.8 → 1.2 → 0.8
- Sound: "alert" SFX plays at telegraph start
- Animation: circle grows from center to full radius over 2 sec

**Player counter:** side-step dodge OUT of red circle before detonation

**Death:** 0.5 sec dissolve + artifact drop (guaranteed artifact)

### 5.4 Boss Enemy (Phase-Based)

**Example: Ice Warden (Phase 1-4 state machine)**

**Phase triggers (HP threshold based):**
- Phase 1: 100%–75% HP (default behavior)
- Phase 2: 75%–50% HP (pattern change)
- Phase 3: 50%–25% HP (increased aggression)
- Phase 4: 25%–0% HP (desperate phase)

**Phase 1 (100–75% HP):**
- Attack pattern: melee swipes + ice projectiles (every 2 sec)
- Projectile: freezing ice sphere, 3 tiles/sec, applies 1 sec slow (50% movement speed)
- Movement: pathfind toward hero, normal speed 1.5 tiles/sec
- Telegraph: none (open phase, learn mechanics)

**Phase 2 (75–50% HP):**
- Attack pattern: melee + AOE frost nova (every 4 sec)
- Frost nova: 3 tile radius, 2 sec telegraph (blue circle with snowflake animation)
- Damage: `2.5 × basic_dmg` to all in radius
- Movement: faster pathfind, 2.0 tiles/sec (matches hero speed)
- New mechanic: summon 2 ice minions (basic melee, 20 HP each)

**Phase 3 (50–25% HP):**
- Attack pattern: rapid ice projectiles (1 per sec) + dash attack (every 3 sec)
- Dash attack: 0.5 sec wind-up, boss charges forward 3 tiles, AOE on landing (2 tile radius)
- Minion summon: every 5 sec, summon 1 ice minion
- Movement: erratic, no fixed pathfind (dodge-heavy phase)
- Telegraph: bright blue flash before dash (0.5 sec warning)

**Phase 4 (25–0% HP):**
- Attack pattern: desperate rapid-fire (all attacks simultaneously)
- Projectile spam: 3 projectiles per sec
- AOE nova: every 2 sec (tighter timing, harder dodge)
- Movement: stationary (stand ground, final stand)
- Minion summon: disabled in phase 4
- Visual: boss model glows bright blue, screen edges tint slightly blue

**Boss attributes:**
- Max HP: `base_boss_hp = 200 + (difficulty × 50)`
- Armor: 10% damage reduction (DR 0.9 multiplier)
- Loot: guaranteed 3× artifact drops on death + 2× gold

**Boss death:**
- Animation: 2 sec dissolve (blue particles fading upward)
- Screen effect: 0.5 sec white fade-to-black transition
- UI: "WAVE COMPLETE" banner, next wave button appears

---

## 6. Damage Calculation

### 6.1 Damage formula

```
final_damage = base_damage 
             × (1 + Σ damage_buffs)           # all synergy buffs cumulative
             × (is_crit ? crit_multiplier : 1)  # crit roll
             × (1 - target_damage_reduction)  # enemy armor DR
             × school_resistance_modifier     # if applicable (D25+ mechanic)
```

**Damage buff sources:**
- Synergy passive bonuses (e.g., red+red = +15% damage)
- Active bursts (Berserk = +80%, Missile Strike = 2.5× base per projectile)
- Artifact perks (up to +50% cumulative per artifact equipped)

**Example calculations:**

| Scenario | Calculation | Result |
|---|---|---|
| Hero base 10 dmg, no buffs, no crit, no DR | 10 × 1.0 × 1 × 1.0 × 1.0 | 10 dmg |
| Hero base 10 dmg, +20% red buff (synergy), crit (×2), 0% DR | 10 × 1.2 × 2.0 × 1.0 × 1.0 | 24 dmg |
| Hero base 10 dmg, Berserk active (+80%), crit, basic enemy no DR | 10 × 1.8 × 2.0 × 1.0 × 1.0 | 36 dmg |
| Missile Strike projectile, hero base 10, 2.5× multiplier, crit (×2), target has 20% DR | 10 × 2.5 × 1.0 × 2.0 × (1 - 0.2) × 1.0 = 10 × 2.5 × 2.0 × 0.8 | 40 dmg |

### 6.2 Crit mechanics

**Crit chance roll:**
- Baseline: 15% crit chance
- Modified by: red school synergy stacking (+3% per red synergy level, max 10 stacking)
- Roll: random per-hit (independent rolls for multi-hit abilities like Missile Strike)

**Crit multiplier:**
- Baseline: 2.0× (200%)
- Modified by: artifact perks (e.g., "Crit deals 250%" = 2.5× multiplier)
- Applied after all other buffs

---

## 7. Combat Timing & Feel

### 7.1 Wave Duration

**Soft target:** 30–60 sec per wave (player-paced)
**Hard cap:** 120 sec per wave (force-fail if exceeded)
**Pause between waves:** 60 sec lobby phase (shop, synergy swap, burst re-select)

**Wave timings:**
- Wave 1–3: ~30 sec (2–3 basic enemies)
- Wave 4–6: ~40 sec (mix of basic + ranged)
- Wave 7–9: ~50 sec (elite + mixed)
- Wave 10 (boss): ~60 sec (1 boss + 2–4 minions)

### 7.2 Animation Lengths

| Action | Duration | Notes |
|---|---|---|
| Hero attack | 0.5 sec | 0.3 sec wind-up + 0.2 sec recovery |
| Hero dodge | 0.2 sec | smooth lateral easing |
| Enemy basic attack | 0.5 sec | 0.4 sec wind-up + 0.1 sec hit |
| Enemy ranged shot | 0.6 sec | 0.3 sec aim + 0.3 sec shoot |
| Enemy AOE telegraph | 2.0 sec | player has 2 sec to dodge |
| Burst activation | 0.3 sec | screen tint + VFX |
| Enemy death dissolve | 0.5 sec | fade + particle burst |
| Artifact drop spawn | 0.3 sec | pop-in + bounce animation |

### 7.3 Camera Shake

| Event | Intensity | Duration | Notes |
|---|---|---|---|
| Hero hit by melee | 4 px | 0.2 sec | light feedback |
| Crit landed | 6 px | 0.3 sec | satisfying impact |
| Boss attack | 8 px | 0.4 sec | weight + threat |
| Burst activation (Missile) | 10 px | 0.4 sec | explosive feel |
| AOE detonation (Elite) | 8 px | 0.3 sec | ground tremor |
| Time Warp freeze | 0 px | – | temporal (no shake) |

**Camera shake algorithm:** Perlin noise-based decay (smooth damping over duration)

---

## 8. Tech Specifications

### 8.1 Frame Rate

**Target:** locked 60 fps on P50 device (iPhone 11+, Snapdragon 765G+)
**Update cycle:** 16.67 ms per frame (60 Hz)
**Physics substep:** 4× per frame (4.17 ms each) for projectile precision

**Graceful degradation:**
- Sustained <60 fps over 3 sec → switch to 30 fps mode (half timestep)
- Sustained <30 fps over 5 sec → show "Stabilizing..." overlay, reduce particle count by 50%
- Sustained <20 fps → combat slow-motion to 50% game speed (preserves playability)

**Measurement:** frame timing via `deltaTime` averaging (exponential moving average, alpha = 0.1)

### 8.2 Input Latency

**Target:** <100 ms tap-to-action (P50 device)
**Swipe-to-dodge:** <100 ms swipe detection to displacement start
**Validation gate:** latency test mandatory before vertical slice greenlight

**Latency optimization:**
- Input polling at 120 Hz (8.3 ms polling rate, independent of render frame rate)
- Touch event batching: process all touches at start of frame (not end)
- Dodge displacement: apply on frame 2 after swipe detection (1 frame latency absorption)

**Measurement:** timestamp delta from touch `DOWN` event to first physics frame applying displacement

### 8.3 Hit Detection

| Object | Size | Shape | Method |
|---|---|---|---|
| Enemy (tap target) | 60×60 px | square collider | circle test (radius 30 px) |
| Hero collision | 1.0 tile radius | cylinder | distance check |
| Projectile | 0.4 tile radius | sphere | per-tile cell collision |
| AOE telegraph | 2.0 tile radius | circle | circle-circle distance |
| Melee attack | 1.0 tile cone forward | cone | cone overlap test |

**Collision checks per frame:** ~O(N) where N = enemy count (max 10 per room)

### 8.4 VFX Budget

**Particle limits:**
- Max simultaneous effects: 50 on-screen
- Per-effect cap: 20 particles max per effect
- LOD system: if ≥30 active effects → reduce particle count per effect by 50% (10 → 5 particles)

**LOD quality tiers:**
- High (60 fps stable): full effect complexity (20 particles, full blur, full brightness)
- Medium (30 fps sustained): 50% particle reduction (10 particles, half blur)
- Low (20 fps sustained): 75% reduction (5 particles, no blur, reduced brightness)

**Quality scale tied to device:**
- P90+ device: high (iPhone 13+, Snapdragon 888+)
- P50 device: medium (iPhone 11, Snapdragon 765G)
- P10 device: low (iPhone 8, Snapdragon 660)

**Particle pooling:** pre-allocate 500 particle slots per frame (reuse across effects)

---

## 9. Edge Cases

| Case | Resolution |
|---|---|
| Hero at corridor edge + swipe left | Dodge blocked, no displacement, animation plays (feedback only) |
| All 3 active slots on cooldown + tap burst | Show "Cooldown" toast (0.5 sec fade), no error sound |
| Enemy stuck in corner (pathfind fails) | Fallback: teleport enemy to nearest valid floor tile after 3 sec stuck |
| Hero hit by 2 attacks in same frame | Damage stacks (additive), both hit numbers displayed |
| Burst activated after wave ends (no enemies alive) | Effect plays on empty field (visual only, no damage dealt) |
| Frame drop >2 sec (>200 ms hitch detected) | Pause combat 0.3 sec, show "Stabilizing..." overlay, resume |
| Multi-touch: 2 fingers swipe + tap | Primary swipe touch wins, secondary tap debounced 100 ms (no simultaneous dodge+burst) |
| Player tap on overlapping enemies (ranged + melee stacked) | Top z-order enemy selected (last rendered = top of stack) |
| Dodge during burst animation | Input queued, dodge executes after burst completes (0.3 sec max queue) |
| Boss phase transition while hero attacking | Boss attack animation cancels, new phase attack starts (smooth transition) |
| Projectile collision while target enemy dies | Projectile continues to dead target location, zero damage applied |
| Shield burst active + hero takes lethal damage | Shield absorbs damage, hero survives at 1 HP, shield breaks |
| Rapid burst re-taps (player mashes icon) | Cooldown checked per tap, excess taps show "Cooldown" toast (no queue buildup) |

---

## 10. Implementation Priority

### Phase 1 — Vertical Slice (Week 1-2)
1. **Hero auto-walk + obstacle avoidance** (basic A* corridor following, simple box collider)
2. **Swipe-to-dodge** (input recognition + side-step animation, 0.2 sec displacement)
3. **Tap focus fire** (target switching, crosshair visualization)
4. **Basic enemy melee AI** (walk toward hero, simple attack animation, 0.5 sec cycle)
5. **Missile Strike burst** (3 homing projectiles, 2.5× damage, basic VFX)

### Phase 2 — Combat Depth (Week 3-4)
6. **Damage calculation system** (synergy buffs, crit chance/multiplier, damage application)
7. **Ranged enemy** (stationary projectile shooter, dodgeable shots)
8. **Hero HP & death state** (HP bar UI, death screen, run end)
9. **Elite AOE telegraph** (2 sec red circle warning, AOE detonation, dodge mechanic)
10. **Barrier Field burst** (blue shield, absorb layer, duration tracking)

### Phase 3 — Ability System (Week 5)
11. **Plague Swarm burst** (3 drone spawns, orbit + attack AI, 8 sec duration)
12. **Berserk burst** (damage buff + HP cost, red aura, 3 sec duration)
13. **Time Warp burst** (enemy freeze, screen effect, 2 sec duration)
14. **Burst cooldown UI** (arc overlay, cooldown timer text, toast notifications)

### Phase 4 — Boss & Polish (Week 6+)
15. **Boss 4-phase state machine** (Ice Warden example: phase detection, AI transitions, minion spawning)
16. **Camera shake feedback** (impact shake, burst activation shake, smooth decay)
17. **Particle effect polish** (school-color VFX, dissolve animations, artifact drop sparkle)
18. **Audio integration** (SFX per action: hit, crit, dodge, burst, enemy attack)
19. **Performance optimization** (LOD systems, particle pooling, input latency testing)

---

## 11. Open Questions

1. **Camera FOV decision** — 60° (performance), 75° (cinematic default), or 90° (wide-screen mobile)? Recommendation: 75° as baseline, allow 60° toggle for low-end devices.

2. **Hero hand visibility** — Always visible during combat, or only during attack wind-up? Recommendation: always visible (immersion + feedback).

3. **Hero idle bob amplitude** — 2 px (subtle), 3 px (noticeable), or 4 px (exaggerated)? Recommendation: 3 px (sweet spot).

4. **Burst activation** — Pause game during 0.3 sec burst animation, or keep real-time? Recommendation: real-time (enemies don't pause, adds tension).

5. **Damage numbers** — Floating text that fades upward, or number ladder (stacking vertically)? Recommendation: floating upward + fade (clarity + readability).

6. **Crit visual** — Golden flash screen effect, or screen shake increase? Recommendation: both (flash at 15% opacity + shake from 4 px → 6 px).

7. **Enemy tap hitbox** — 60×60 px adequate for all enemy sizes, or scale per enemy? Recommendation: fixed 60×60 px (consistent hit area, easier QA).

8. **Multi-enemy tap priority** — Stack-prioritize (top z-order), or geometry-prioritize (closest to tap point)? Recommendation: stack z-order (simpler, matches common mobile UX).

9. **Input buffer windows** — 33 ms for dodge timing, or increase to 50 ms for mobile tolerance? Recommendation: 33 ms baseline, test on P10 devices and adjust if needed.

10. **Boss minion behavior** — Do minions target hero or assist boss? Recommendation: minions target hero independently (multi-threat complexity).

---

## 12. Reference Links

- **01_CORE_LOOP.md** — Wave structure, lobby phase, run progression
- **02_BIOME_01.md** — Ice Warden boss spec, room layout, difficulty curve
- **04_SYNERGY_SYSTEM.md** — School buffs, damage multipliers, burst synergies
- **06_ARTIFACT_SYSTEM.md** — Equipment perks, damage modifiers, loot tables
