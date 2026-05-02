# 06 — Hero Board Progression

## 1. Концепция Hero Board

Hero Board — это persistent UI экран в Hub. Игрок видит свой "deck" permanent perks и slots. Каждый Day-X unlock = новая ячейка/способность. НЕ stat bumps, а механические gameplay changes.

Цель: каждые 5-7 дней игрок открывает новый layer гейплея — поддерживает novelty curve. Это основной инструмент удержания игроков в долгосрочной перспективе и создания sense of progression beyond simple numerical scaling.

## 2. Day-by-day unlock schedule

| Day | Unlock | Что меняется в gameplay |
|---|---|---|
| D1 | Tutorial complete, 1 active slot, only Red school | Стартовое состояние |
| D5 | **Slot 1 unlock + Blue school** | 2 active slots в combat, Blue artifacts начинают дропать |
| D10 | **Artifact Transmutation UI** | Merge 3 identical tier-3+ → random tier-4 (gambling) |
| D14 | **Slot 2 unlock + Green school** | 3 active slots, Green artifacts дропают |
| D17 | **Artifact Reroll** | 1× per session, swap 1 active slot ↔ backpack alternative |
| D21 | **Hero Board Perma Passive (slot 1 of 3)** | 1 permanent synergy locked, never expires between runs |
| D25 | **Synergy Stacking Bonus** | +5% damage per active cross-school combo, max 3 combos = +15% |
| D30 | **Chaos Slot + Cross-school triple combos** | 4-th active slot + tier-4 cross combos enabled (Prismatic Burst etc) |

## 3. Detailed unlock specs

### 3.1 D5: Slot 1 + Blue School

**Status Change:**
- Перед D5: hero имеет только 1 active slot, Red artifacts only
- After D5: 2 active slots доступны, Blue artifacts начинают дропать в Ice Castle (60% blue probability)

**UI Change:** 
- 2nd slot icon появляется в нижнем center экрана во время combat
- Анимация reveal при разблокировке (частицы, звук)

**Player Benefit:** 
- Можно строить комбинации между Red и Blue школами
- Возможность tank hits через Barrier Field burst (Blue synergy)
- Расширение тактических опций в combat

**Progression Trigger:**
- Автоматический unlock на 5-й день игры
- Notification в Hub: "Новый слот и школа магии разблокированы!"

### 3.2 D10: Artifact Transmutation

**Location & UI:**
- Hub получает new menu item "Transmutation Altar"
- В лобби-фазе до старта волн появляется button "Transmute 3 → 1"

**Mechanic Details:**
- Player отбирает 3 identical tier-3+ артефакта из backpack
- Нажимает "Transmute" → spin animation 1.5 sec
- Output: random tier-4 артефакт (60% cross-school weighted, 40% pure school weighted)
- Результат попадает в backpack immediately

**Cooldown & Balance:**
- 1× per session (refreshes на next day OR после wave 10+ completion)
- Предотвращает exploits, но дает достаточно uses для meaningful progression

**Visual Design:**
- Golden mortar-and-pestle UI element
- Particle effects: blue/gold sparkles во время transmutation
- Sound: mystical chime на completion

**Discovery Moment:**
- Туториал-хинт на D10: "Теперь ты можешь комбинировать лишние артефакты для получения редких вещей!"
- Поощряет экспериментировать с gambling mechanic

### 3.3 D14: Slot 2 + Green School

**Status Change:**
- 3 active slots total становятся доступны
- Green artifacts появляются в drops (особенно в Forest и Nature biomes)
- Green-specific synergies разблокируются для выбора

**School-Specific Synergies (Green):**
- Plague Doctor: inflict poison stacks on hit
- Lifethorn: heal 5% damage dealt
- Nature's Embrace: +20% artifact recharge speed

**UI Change:** 
- 3rd slot icon появляется в combat UI
- Hero Board визуально обновляется со всеми тремя школами

**Player Benefit:** 
- Full school diversity now possible
- Можно строить настоящие cross-school synergies (Red+Blue+Green combos)
- Opens strategic depth: need to balance all three schools or specialize

**Strategic Implication:**
- До D14 игрок мог фокусироваться на Red/Blue combo
- После D14 optimal builds требуют thinking в 3D (3 active slots simultaneously)

### 3.4 D17: Artifact Reroll

**Location & Mechanic:**
- New button "Reroll" в combat UI (нижний right corner)
- Доступно 1× per session
- Swap 1 любой active slot artifact на artifact из backpack (if available)

**Use Case:**
- Ситуация: bad artifact luck, получил неполезный tier-2 в slot 1
- Solution: reroll его на tier-3 из backpack mid-combat
- Reduces frustration от RNG, adds tactical flexibility

**Technical Details:**
- Animation: 0.5 sec swap effect с звуком
- Cooldown: 1 use per combat session
- Refreshes на next combat session или после reset
- Не может reroll в пустой slot (backpack должен иметь альтернативу)

**Balance Notes:**
- Ограничение в 1 use/session предотвращает trivializing всех bad pulls
- Но дает real agency в moments of need

### 3.5 D21: Perma Passive (Slot 1 of 3)

**What It Is:**
- Hub получает new dedicated tab "Hero Board" с 3D visualization
- Slot 1 разблокируется на D21
- Игрок выбирает 1 синергию для permanent activation

**Mechanic:**
- Выбранная synergy всегда active в combat
- Не зависит от backpack content или текущих active slots
- Остается locked до следующей deliberate change (player-initiated)

**Example Synergies:**
- Crimson Aegis lock: всегда +10% vampirism (heal 10% damage dealt)
- Glacial Ward lock: всегда +12% damage reduction from all sources
- Wildgrowth lock: всегда +8% artifact recharge speed
- Shadow Dance lock: всегда 15% chance to dodge attacks

**Progressive Unlocks:**
- Slot 1 at D21
- Slot 2 at D25
- Slot 3 at D30

**Strategic Implications:**
- Defines build identity and playstyle
- Encourages experimenting with different locked synergies
- Changes game feel significantly
- Players lock into favorite combos = increased attachment

**UI Display:**
- Three golden rings around hero avatar
- Active locks shown as "glowing" ring icons
- Lock can be changed anytime from Hero Board menu
- Change takes effect next combat run

### 3.6 D25: Synergy Stacking Bonus

**Mechanic:**
- Каждый active cross-school combo, работающий одновременно = +5% global damage
- Max 3 combos = +15% total damage multiplier

**Definition of "Active Combo":**
- Red artifact + Blue artifact both in active slots = R+B combo active
- Red artifact + Green artifact both active = R+G combo active
- Blue artifact + Green artifact both active = B+G combo active
- Triple (R+B+G) = counts as 3 combos? NO, counts as 1 triple + potential for 2 pairs

**Examples:**
- Setup A: Red + Blue + (empty) = 1 combo = +5% damage
- Setup B: Red + Blue + Green = 3 combos (R+B, R+G, B+G) = +15% damage
- Setup C: Red + Red + Blue = only R+B combo = +5% damage (pure Red doesn't combo with Red)

**Visual Feedback:**
- Stack counter top-left of screen: "Combos: 2/3"
- Golden glow effect when 3 combos active
- Damage numbers display colored bonus when stacking active

**Player Discovery:**
- Encourages diverse artifact builds
- Rewards planning ahead (collecting artifacts from all 3 schools)
- Makes each school valuable = no "dump stat" school

### 3.7 D30: Chaos Slot + Tier-4 Cross Combos

**4th Active Slot:**
- Fourth active slot appears (rightmost in UI)
- Allows 4 simultaneous artifacts
- All previous combo stacking rules apply (+20% max damage with full diversity)

**Tier-4 Cross-School Combos:**
These are special effects unlocked when specific combinations of schools are active:

- **Prismatic Burst** (R+B+G in rotation): every 8 attacks, emit AOE burst healing 10% HP to hero
- **Void Anchor** (B+G+R sequence): stun next elite enemy that enters for 1 wave
- **Inferno Cascade** (R+R+B): R attacks chain to +2 additional enemies, gain +20% fire damage
- **Tideshift** (G+R+G+B in order): 4-artifact sequences trigger water shield blocking 1 attack
- **Chronofracture** (3+ Blue pure): Blue artifacts recharge 30% faster, cooldowns visible to player

**Chaos Wave Mechanic:**
- Every 20 waves completed, game triggers "Chaos Wave"
- Chaos Wave lasts 3 waves
- During Chaos Wave: all enemies +50% movement speed + +25% attack speed
- Forces adaptive play: can't just farm same strategy
- Provides skill check and difficulty spike

**End-Game State:**
- D30 = maximum complexity available in baseline progression
- Players can spend weeks mastering D30 state before prestige
- All three schools fully utilized
- Strategic depth reaches peak

## 4. UI: Hero Board screen

**Location:**
- Main Hub menu, accessible from central navigation

**Visual Design:**
- Center: 3D rotating hero avatar (matches character class)
- Background: animated cosmic theme (gold/purple particles)

**Perma Slots Section:**
- Three large circular rings around hero
- Each ring represents 1 perma slot
- Locked rings: greyed out, show "Unlocks at Day X"
- Unlocked rings: glowing gold, show current locked synergy name + icon
- Click to change locked synergy (opens selection modal)

**Right Panel: Unlock Timeline**
- Vertical scrollable list of all unlocks (D1 → D30)
- Format per row:
  - Day number (D5)
  - Unlock name (Slot 1 + Blue School)
  - Status badge (Locked / Unlocked / Coming in X days)
  - Brief description
- Click any unlock to see detailed explanation

**Bottom Section: Soul Points Spending**
- Cosmetic upgrades for Hero Board appearance
- Examples: hero avatar skin, border glow, unlock particle effects
- Spent from Soul Points (earned through gameplay)
- Cosmetic-only, no mechanical advantage

## 5. Acceleration через Cosmic Shards (post-Prestige)

После Prestige (см. 07_PRESTIGE_META) игрок получает Cosmic Shards. Может тратить на:

| Cost | Unlock | Effect |
|---|---|---|
| 100 CS | Skip D1 tutorial | Start at D5 progression (slots, schools) |
| 250 CS | Fast-forward to D10 | Transmutation immediately available |
| 500 CS | Custom starting school | Choose Red/Blue/Green as primary on reset |
| 1000 CS | Hero Board Slot 4 | Unlock 4th active combat slot (permanent) |
| 2500 CS | Always-On Stacking Bonus | Synergy stacking +15% always active, independent of waves |
| 5000 CS | Master Reset | Choose ANY 1 unlock from D1-D30 to start with immediately |

**Design Notes:**
- Each cost reflects power level gained
- Higher costs prevent pay-to-win; still requires planning
- Cosmics Shards are premium currency (earned through Prestige only)
- Purchases are permanent per prestige cycle

## 6. Open questions

1. **Login Days vs Playtime:** Hero Board unlocks tied to calendar days or cumulative playtime hours? (Recommendation: calendar days to encourage daily habit, but add playtime catch-up for lapsed players)

2. **Skip Days Handling:** If player doesn't login for 3 days, do unlocks auto-trigger or wait for daily login? (Recommendation: auto-trigger on return to maintain progression momentum)

3. **Cosmic Shards Rate:** What's realistic conversion? (Example: 1 prestige cycle = ~500-1000 CS earned through gameplay, making purchases aspirational)

4. **Perma Slot Reset:** Can player change locked synergy multiple times, or permanent per cycle? (Recommendation: unlimited changes from Hero Board menu, no cooldown, to encourage experimentation)

5. **Chaos Wave Opt-In:** D30 Chaos Wave mandatory or can players disable for casual runs? (Recommendation: mandatory in story/progression, optional in endless mode)

6. **Mobile UI:** Hero Board screen on mobile — fullscreen dedicated view or modal popup? (Recommendation: fullscreen tab due to complexity of 3D visualization + timeline)

7. **Cross-Combo Detection:** How does game detect "R+B+G sequence" vs "R+B+G any order"? (Recommendation: any order within active wave counts, order-based only for special tier-4 combos like Tideshift)

8. **Perma Slot Cosmetics:** Can player customize perma slot appearance (color, icon, name)? (Recommendation: yes, from cosmetics shop for Soul Points or Cosmic Shards)
