# 09 — UI/UX Specification

## 1. Screen Inventory

Полный список major screens в игре:

1. **Splash / Loading** — game launch, asset preload progress
2. **Main Menu / Hub** — между runs, главное меню
3. **Hero Board** — meta progression view, unlock history
4. **Workshop** — Soul Points spending, artifact transmutation
5. **Codex** — discovered synergies browser, 24 schools database
6. **Cosmic Altar** — Prestige menu, permanent upgrades
7. **Expedition Start** — pre-run loadout selection, difficulty choice
8. **Lobby Phase UI** — merge backpack UI overlay over hero
9. **Combat Phase UI** — first-person HUD, enemy targeting
10. **Boss Encounter UI** — combat + boss-specific elements (HP bar, phase)
11. **Carry-Over Screen** — modal post-boss, artifact selection
12. **Event Node Screen** — modal between biomes, choice event
13. **Run Modifier Choice** — modal D25+ every 10 waves
14. **Game Over / Death Screen** — fail state, soul points earned
15. **Run Complete** — success state, final stats
16. **Settings** — audio, controls, accessibility options
17. **Battle Pass** — cosmetic progression, seasonal rewards
18. **Leaderboard / Weekly Challenge** — competitive view, rankings
19. **Daily Goal** — daily quest tracker, streaks
20. **Hero Profile** — stats history, run analytics

## 2. Hub Screen (Main Menu)

Главный screen между runs. Layout portrait, safe area respecting notch.

```
┌─────────────────────────────────────┐
│  [Avatar] HeroName  [Settings] [⚙] │ <- Top bar (60px)
│  Soul Points: 1,234   CS: 250       │ <- Currency display
├─────────────────────────────────────┤
│                                     │
│       [3D Hero Avatar]              │ <- Hero showcase (40% screen)
│                                     │
│  ┌────────┐  ┌────────┐  ┌────────┐│
│  │ Daily  │  │ Weekly │  │Battle  ││ <- Quick action tiles
│  │ Goal   │  │ Chall. │  │ Pass   ││
│  └────────┘  └────────┘  └────────┘│
│                                     │
├─────────────────────────────────────┤
│  ┌─────────────────────────────┐   │
│  │  ▶ START EXPEDITION          │   │ <- Primary CTA (golden)
│  └─────────────────────────────┘   │
├─────────────────────────────────────┤
│ [Hero] [Workshop] [Codex] [Cosmic]  │ <- Bottom nav (5 icons)
└─────────────────────────────────────┘
```

### Element Specifications

- **Top bar:** 60px height, semi-transparent dark (#1A1A2E CC), safe area padding top 12px
- **Currency display:** Soul Points (gold icon) + Cosmic Shards (purple icon), right-aligned
- **Hero avatar:** 3D model, centered, ~40% screen height, continuous idle animation
- **Quick action tiles:** 100×100 px each, 3 across center, spacing 12px between
- **Start Expedition button:** 80% screen width, 60px height, golden gradient (#FFC107), rounded 8px
- **Bottom nav:** 5 icons × 60px, evenly spaced, 80px total height including safe area

### Interactions

- Avatar tap: open Hero Profile with stats
- Quick tiles: navigate to Daily Goal, Weekly Challenge, Battle Pass
- Start Expedition: transition to Expedition Start screen
- Bottom nav: navigate to respective screens

## 3. Lobby Phase UI (Merge Backpack)

Overlay на first-person view (герой стоит перед закрытой дверью). Layer order: world → hero → UI overlay.

```
┌─────────────────────────────────────┐
│  Wave 3/10  Ice Castle  [Skip] 0:42 │ <- Top status bar
├─────────────────────────────────────┤
│  [▼][▼][▼][▼][▼][▼]                │ <- Artifact queue (6 visible)
├─────────────────────────────────────┤
│       ┌───┬───┬───┐                 │
│       │ R │   │ B │                 │
│       ├───┼───┼───┤                 │
│       │ R │ G │ B │   Synergy:      │ <- 3×3 backpack grid
│       ├───┼───┼───┤   Red 2/3       │ <- Merge mechanic
│       │   │   │ R │   Blue 2/3      │
│       └───┴───┴───┘   Green 1/3     │
│                                     │
│  ┌────┐ ┌────┐ ┌────┐              │
│  │ R  │ │ B  │ │ —  │  <- 3 active slots (golden border)
│  │ ⚔  │ │ 🛡 │ │    │  <- Burst icon below
│  └────┘ └────┘ └────┘              │
│                                     │
│  [Door visible behind UI - locked]  │
└─────────────────────────────────────┘
```

### Specifications

- **Backpack grid:** 240×240 px, centered, spacing 4px between cells
- **Cell size:** 80×80 px each, colored background + school icon
- **Artifact queue:** top section, 6 items × 60px wide, horizontal scroll if needed
- **Active slots bottom:** 100×100 px each, gap 20px between, golden border 2px when active
- **Synergy progress:** right column, ~80px wide, progress bar + text label
- **Skip button:** top right, available after 15 sec, tap to skip wave
- **Timer:** top right, large bold numbers, color transition: white → orange (10s) → red (5s)
- **Background:** semi-transparent dark with corner vignette

### Interactions

- Drag artifact from queue → grid cell to place
- Drag grid artifact → queue to return (if not merging)
- Merge trigger: 3 artifacts of same school → auto-merge animation (0.6 sec)
- Active slot icon tap: view cooldown, school details
- Skip button: skip lobby (timer updates in real-time)

## 4. Combat Phase UI (First-Person HUD)

```
┌─────────────────────────────────────┐
│  HP: ██████░░  Wave 5/10  Kills: 7  │ <- Top HUD (40px)
├─────────────────────────────────────┤
│                                     │
│        [Enemy 1]                    │
│                       [Enemy 2]     │
│   [Enemy 3]                         │ <- First-person view
│                                     │
│        [Hero crosshair]             │
│                                     │
│   [Hero hands/weapon]               │
│                                     │
├─────────────────────────────────────┤
│  ┌────┐ ┌────┐ ┌────┐              │
│  │ ⚔  │ │ 🛡 │ │ ☣ │  <- Active slot icons
│  │ 5s │ │ R  │ │ 12s│  <- Cooldown / Ready badge
│  └────┘ └────┘ └────┘              │
│                                     │
│  [Synergy badges floating left]     │ <- Top 3 active synergies
└─────────────────────────────────────┘
```

### Specifications

- **Top HUD:** 40px height, HP bar (gradient red #E53935 → yellow #FFC107 → green #43A047), wave counter, kills counter
- **HP bar:** 120px wide, rounded 4px, border #FFFFFF 1px
- **Camera area:** ~70% screen height, FOV 75°, damage particles, muzzle flash
- **Enemy hitboxes:** 60×60 px tap target each, transparent overlay
- **Active slot icons:** 100×100 px at bottom, cooldown ring overlay (circular progress)
- **Cooldown text:** bold 16px, color white/red based on ready status
- **Synergy badges:** small icons left side (24×24 px each), max 3 visible simultaneously, fade in/out
- **Damage numbers:** float up + fade out animation 1.5 sec, color by school

### Interactions

- Tap enemy: attack target, trigger projectile + hit feedback
- Swipe dodge mechanic: swipe left/right to dodge incoming projectiles
- Active slot: auto-fire on cooldown (or tap for manual trigger if burst mode)
- Long-press active slot: view synergy details, cooldown info

## 5. Carry-Over Screen (Modal)

Post-boss choice modal. User selects 3 artifacts to keep for next biome.

```
┌─────────────────────────────────────┐
│  BIOME 1 CLEARED!                   │
│  Choose 3 artifacts to carry        │
├─────────────────────────────────────┤
│                                     │
│  ┌───┬───┬───┐                     │
│  │ R★│ B │ R │   <- Tap to select  │
│  ├───┼───┼───┤      (golden border │
│  │ B★│ G │ B★│       = selected)   │
│  ├───┼───┼───┤                     │
│  │ R │ G │ B │                     │
│  └───┴───┴───┘                     │
│                                     │
│  Selected: 3/3                      │
│  Lost artifacts → 60 Soul Points    │
│                                     │
│  [Watch Ad +1 carry slot]  [✓ Confirm] │
│                                     │
│  Auto-confirm in 0:15               │
└─────────────────────────────────────┘
```

### Specifications

- **Modal background:** semi-transparent dark (#1A1A2E DD), 0.7 opacity with Gaussian blur
- **Grid:** 3×3 artifact display, cell size 80×80 px, spacing 4px
- **Selected indicator:** golden border 3px + star icon top-right corner
- **Counter:** "Selected: X/3" text, color green when full
- **Soul Points info:** "Lost artifacts → Y Soul Points" (calculated per artifact)
- **Ad button:** secondary style, 50×50 px icon, gray background
- **Confirm button:** primary golden style, 60px height, right-aligned
- **Auto-confirm timer:** countdown display, cancellable by user interaction
- **Slide duration:** modal enters with slide-up animation 0.4 sec

### Interactions

- Grid cell tap: toggle selection (max 3 selected)
- Ad button: watch rewarded video, gain +1 carry slot temporarily
- Confirm button: proceed to next biome with selected artifacts
- Auto-confirm: if user inactive for 15 sec, confirm automatically

## 6. Event Node Screen (Modal)

Between-biome event modal. Three choice cards revealed, tap to select.

```
┌─────────────────────────────────────┐
│  EVENT NODE                         │
│  Choose your fate                   │
├─────────────────────────────────────┤
│                                     │
│  ┌────────┐ ┌────────┐ ┌────────┐  │
│  │ 🏆     │ │ 💀     │ │ 🔧     │  │
│  │Treasure│ │Cursed  │ │Vendor  │  │
│  │Vault   │ │Shrine  │ │        │  │
│  │+t3 +50 │ │+t4 -X  │ │transmute│  │
│  └────────┘ └────────┘ └────────┘  │
│                                     │
│  Tap to select                      │
└─────────────────────────────────────┘
```

### Specifications

- **Modal:** 80% screen width, centered, slide-up animation 0.4 sec
- **Card size:** 100×140 px each, rounded 8px, spacing 12px
- **Card background:** gradient dark → medium based on event type
- **Icon:** 40×40 px centered top, emoji or custom icon
- **Title:** bold 14px, center-aligned
- **Description:** regular 12px, secondary text color
- **Reward preview:** bold 12px, color matches reward type (gold/purple/etc)
- **Hover state:** scale 1.05, shadow increase
- **Selection feedback:** bounce animation 0.3 sec + transition to next node

### Interactions

- Card tap: select event, trigger reward/penalty, proceed to next node
- No selection timeout: auto-select random option after 20 sec
- Event execution: apply buffs/debuffs, next wave triggers

## 7. Run Modifier Choice (D25+ Modal)

Every 10 waves starting D25, user chooses from 3 random modifiers.

```
┌─────────────────────────────────────┐
│  RUN MODIFIER CHOICE                │
│  Select 1 modifier                  │
├─────────────────────────────────────┤
│                                     │
│  ┌────────┐ ┌────────┐ ┌────────┐  │
│  │ +50% HP│ │-30% ATK│ │+Gold x2│  │
│  │ EASY   │ │HARD    │ │REWARD  │  │
│  │        │ │        │ │        │  │
│  └────────┘ └────────┘ └────────┘  │
│                                     │
│  Tap to select                      │
└─────────────────────────────────────┘
```

### Specifications

- **Modal:** 80% screen width, centered
- **Card size:** 100×140 px each, rounded 8px
- **Difficulty badge:** top-left corner, color-coded (green EASY, red HARD, gold REWARD)
- **Effect text:** bold 14px, primary text
- **Category text:** medium 12px, secondary color
- **Selection:** tap to apply modifier, game resumes wave
- **No timeout:** player must actively choose

### Interactions

- Card tap: apply modifier immediately, wave resumes
- Modifier effect: applied to current and all subsequent waves until run end

## 8. Hero Board Screen

Hub menu screen showing meta progression.

### Layout

```
┌─────────────────────────────────────┐
│  [← HERO BOARD]          [Hero Name]│
├─────────────────────────────────────┤
│  Permanent Unlocks                  │
│  ┌───────────────────────────────┐  │
│  │ [Lock] Backpack Slot +1       │  │ <- Upgrade rows
│  │ [Lock] Active Slot 2/3        │  │
│  │ [✓] Codex School: Red         │  │
│  │ [✓] Carry-Over: +1 slot       │  │
│  └───────────────────────────────┘  │
│                                     │
│  Cosmic Shards: 12                  │ <- Prestige currency
│  [Spend at Cosmic Altar]            │
└─────────────────────────────────────┘
```

### Specifications

- **Unlock list:** scrollable section, row height 56px
- **Lock icon:** gray if locked, green check if unlocked
- **Cost display:** "500 Cosmic Shards" in secondary text
- **Tap locked upgrade:** show cost modal, confirm spend
- **Tier system:** visual progression from unlocks

## 9. Codex Screen

Browse all 24 synergies, discovered (full info) and locked (silhouette).

### Layout

```
┌─────────────────────────────────────┐
│  [← CODEX]                          │
├─────────────────────────────────────┤
│  RED (8 discovered, 2 locked)       │
│  ┌─────────────────────────────┐   │
│  │ [Icon] Synergy Name         │   │
│  │ Description text (2 lines)  │   │
│  │ School: Red | Level 3       │   │
│  └─────────────────────────────┘   │
│                                     │
│  BLUE (6 discovered, 4 locked)      │
│  [Similar layout]                   │
│                                     │
│  GREEN (7 discovered, 3 locked)     │
│  [Similar layout]                   │
└─────────────────────────────────────┘
```

### Specifications

- **School sections:** grouped by Red/Blue/Green
- **Card size:** 100% width, ~80px height, padding 12px
- **Discovered card:** full icon, name, 1-2 line description, stats
- **Locked card:** silhouette icon + "?" + hint text (how to unlock)
- **Tap card:** open detail modal with full synergy info

## 10. Workshop Screen

Soul Points spending menu for artifact transmutation and upgrades.

### Layout

```
┌─────────────────────────────────────┐
│  [← WORKSHOP]      Soul Points: 500 │
├─────────────────────────────────────┤
│  Transmutation                      │
│  ┌─────────────────────────────┐   │
│  │ Red → Blue (500 SP)         │   │
│  │ Blue → Green (500 SP)       │   │
│  │ Green → Red (500 SP)        │   │
│  └─────────────────────────────┘   │
│                                     │
│  Artifact Reroll                    │
│  ┌─────────────────────────────┐   │
│  │ Backpack: Reroll (100 SP)   │   │ <- Cost decreases after reroll
│  │ Queue: Reroll (150 SP)      │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘
```

### Specifications

- **Menu items:** row-based list, 56px height each
- **Cost badge:** right-aligned, gold color with icon
- **Disabled state:** grayed out if insufficient Soul Points
- **Tap item:** confirm dialog with "Spend X SP?" prompt

## 11. Cosmic Altar Screen

Prestige menu for permanent upgrades using Cosmic Shards.

### Specifications

- **Layout:** similar to Hero Board
- **Prestige tree:** visual display of unlocked perks
- **Shard counter:** prominent display of available shards
- **Upgrade cost:** scales with tier (first: 100, second: 250, etc.)

## 12. Settings Screen

- **Audio:** Master / Music / SFX sliders (0-100%)
- **Controls:** Sensitivity slider for swipe-dodge (0.5x - 2.0x)
- **Accessibility:** 
  - Color-blind mode toggle (red/blue/green → patterns)
  - Reduced motion toggle (disable camera bob, simplify particles)
  - Text scaling (100% / 110% / 120% / 130%)
- **Notifications:** Daily reminder ON/OFF
- **Account:** Sync progress / Cloud save status / Logout

## 13. Touch Target Sizes (Mobile UX Standard)

| Element | Min Size | Actual |
|---|---|---|
| Primary button | 44×44 px | 60×60 px |
| Secondary button | 44×44 px | 50×50 px |
| Icon button (active slot) | 44×44 px | 100×100 px |
| Backpack cell | 44×44 px | 80×80 px |
| Enemy hitbox (combat) | 44×44 px | 60×60 px |
| Card (event/modifier) | 44×44 px | 100×140 px |
| Bottom nav icon | 44×44 px | 60×60 px |

All targets exceed Apple HIG 44px minimum. Spacing between targets: min 8px.

## 14. Animation Guidelines

| Animation | Duration | Easing |
|---|---|---|
| Screen transition | 0.3 sec | Ease-out |
| Button tap feedback | 0.1 sec | Spring (0.95 scale) |
| Modal open/close | 0.4 sec | Ease-out |
| Merge animation | 0.6 sec | Ease-out (3D spin) |
| Burst activation flash | 0.3 sec | Linear |
| Damage number float | 1.5 sec | Ease-in-out (cubic) |
| Carry-over reveal | 0.8 sec | Ease-out |
| Synergy badge pop-in | 0.3 sec | Spring |
| Enemy death particle | 0.5-1.0 sec | Variable |
| Cooldown ring tick | 0.2 sec | Linear |

## 15. Color Palette

| Role | Hex | Use Case |
|---|---|---|
| Primary (CTA) | #FFC107 | Buttons, highlights, active state |
| Red school | #E53935 | Damage, offense, red artifacts |
| Blue school | #1E88E5 | Defense, blue artifacts |
| Green school | #43A047 | Sustain, healing, green artifacts |
| Background dark | #1A1A2E | Main bg, modals |
| Background medium | #2C2C44 | Secondary areas, cards |
| Text primary | #FFFFFF | Headers, body text |
| Text secondary | #B0BEC5 | Subtitles, tooltips |
| Warning | #FF9800 | Timer (10s), cautions |
| Danger | #D32F2F | Timer (5s), low HP |
| Success | #4CAF50 | Confirmations, completed |
| Neutral | #757575 | Disabled, inactive |

All colors tested for WCAG AA contrast (4.5:1 minimum for text).

## 16. Typography

| Style | Font | Size | Weight | Use |
|---|---|---|---|---|
| Header | Roboto | 24-32px | Bold | Screen titles, modals |
| Body | Roboto | 14-16px | Regular | Main text, descriptions |
| Number | Roboto Mono | 18-22px | Bold | HP, scores, cooldowns |
| Subtitle | Roboto | 16px | Medium | Section headers, labels |
| Tooltip | Roboto | 12px | Regular | Small info, hints |
| Button | Roboto | 14px | Medium | CTA text |

Line height: 1.4× font size. Letter spacing: normal (0px).

## 17. Accessibility Features

- **Color-blind mode:** school colors replaced with patterns
  - Red = solid fill
  - Blue = striped (horizontal)
  - Green = dotted grid
- **Reduced motion:** 
  - Disable camera bob in combat
  - Simplify particle effects (fewer particles)
  - Slower animations (1.5× duration)
- **Text scaling:** system text scale respected up to 130%
- **Tap targets:** all interactive elements min 44×44 px (Apple/Google HIG)
- **High contrast:** text colors exceed 4.5:1 WCAG AA
- **Labels:** all buttons have text labels or aria-labels
- **Audio:** no critical game information in audio only (all have visual feedback)

## 18. Screen Flow Diagram

```
[Splash] → [Hub]
            ├── [Hero Board] → back to Hub
            ├── [Workshop] → back to Hub
            ├── [Codex] → back to Hub
            ├── [Cosmic Altar] → [Prestige Confirmation] → back to Hub
            ├── [Settings] → back to Hub
            ├── [Daily Goal] → back to Hub
            ├── [Weekly Challenge] → back to Hub
            ├── [Battle Pass] → back to Hub
            └── [Start Expedition]
                  ↓
                [Expedition Start] (loadout select)
                  ↓
                [Lobby Phase] ⇄ [Combat Phase]  (10 cycles)
                  ↓
                [Boss Encounter] (wave 10)
                  ↓
                [Carry-Over Screen] (select 3 artifacts)
                  ↓
                [Event Node Screen] (choose event)
                  ↓
                [Lobby Phase] ⇄ [Combat Phase]  (10 cycles)
                  ↓
                [Run Modifier Choice] (D25+, every 10 waves)
                  ↓
                ... (cycle repeats)
                  ↓
                [Run End Screen]
                  ├─ [Death Screen] (if HP ≤ 0)
                  └─ [Completion Screen] (if final boss defeated)
                  ↓
                [Hub]
```

## 19. Responsive Breakpoints

| Breakpoint | Screen | Example Devices |
|---|---|---|
| Small | 375×667 | iPhone SE (2nd gen) |
| Standard | 393×852 | iPhone 14, Pixel 6a |
| Large | 412×915 | iPhone 14 Pro Max, Pixel 6 Pro |
| Tablet | 600×800 | iPad Mini, Galaxy Tab A |

Primary target: **Standard (393×852)** — iPhone 14. Min supported: **Small (375×667)** — iPhone SE.

All UI scales proportionally. Safe area: 16px padding all sides (respects notch).

## 20. Performance Targets

- **Lobby Phase UI:** 60 FPS, <5ms frame time
- **Combat Phase HUD:** 60 FPS, <5ms frame time
- **Modal transitions:** 30 FPS acceptable (non-critical)
- **Memory:** <300 MB for active scene
- **Battery:** <10% drain per hour at median gameplay
- **Thermal:** device <43°C sustained

Tested on iPhone 11+ (P50 device) and Snapdragon 765+ (mid-range Android).

## 21. Open Questions & TBD

1. **Camera FOV for combat** — 60° (narrow) / 75° (current) / 90° (wide)?
2. **Active slot icons** — school color icons or burst type icons?
3. **Synergy badges display** — full list visible or top 3 active only?
4. **Backpack grid orientation** — centered or left-offset layout?
5. **Modal background opacity** — 0.5 / 0.7 / 0.9 (affects readability)?
6. **Hub 3D avatar animation** — continuous idle loop or static pose?
7. **Notifications for retention** — push triggers (daily reset, run completion, etc.)?
8. **Carry-over screen timeout** — 15 sec auto-confirm or longer/shorter?
9. **Enemy particle count** — high quality (many) or mobile optimized (few)?
10. **Audio SFX priority** — all SFX mixed or selective culling under CPU load?

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-30  
**Status:** Ready for Prototyping  
**Next Phase:** Wireframe refinement, animation specification
