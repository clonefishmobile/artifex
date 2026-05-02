# 07 — Prestige Meta System

## 1. Концепция Prestige

Prestige — это **reset с benefits**. Игрок жертвует current progression (артефакты, level героя, current biome unlock state) в обмен на **Cosmic Shards** — permanent meta-currency которая разблокирует фундаментальные изменения стартовых правил.

**Цель**: создать "endless replayability через permanent reshape rules" — каждые 3-5 prestige cycles игрок имеет fundamentally different game (новая стартовая backpack shape, custom starting school, accelerated progression).

## 2. Eligibility — когда игрок может Prestige?

**Условие 1 (default):** completed 3 successful expeditions (5 биомов cleared)
**Условие 2 (alternative):** accumulated 5000+ Soul Points
**Условие 3 (premium):** $19.99 Hero Ascension Fast-Track IAP — instant prestige eligible at any time

Когда eligible — Hub UI показывает new "Prestige" button (golden glow) + notification.

## 3. Prestige flow

```
Hub → Cosmic Altar menu
   ↓
Prestige Confirmation Screen
   ├─ "You will lose: 9 артефактов в backpack, hero level X, hero board unlock state"
   ├─ "You will gain: Y Cosmic Shards (calculated from Soul Points + biomes cleared)"
   └─ "Permanent unlocks remain: previous Cosmic Shards purchases, completed biomes (history)"
   ↓
Player taps "PRESTIGE" → cinematic 5 sec animation
   ├─ Hero glows golden, dissolves
   ├─ Cosmic Shards spawn from dissolved hero
   └─ Hero respawn in Hub at "Day 1" state (но с Cosmic Shards + unlocks)
   ↓
Returns to Hub с new abilities + Cosmic Shards available для spending
```

## 4. Cosmic Shards earning formula

```
Cosmic Shards earned per Prestige = 
    (total Soul Points accumulated × 0.001) 
  + (biomes cleared this prestige cycle × 50)
  + (boss kills × 25)
  + (synergies discovered × 5)
```

**Example:** Игрок прошёл 3 expeditions (15 биомов total), accumulated 7500 Soul Points, killed 15 bosses, discovered 20 synergies:
- 7500 × 0.001 = 7.5 → 7 CS
- 15 × 50 = 750 CS
- 15 × 25 = 375 CS
- 20 × 5 = 100 CS
- **Total: 1232 CS**

Realistic first prestige earnings: 800-1500 Cosmic Shards. Subsequent prestige: 1200-2500.

## 5. Cosmic Shards spending — Permanent Unlocks Menu

| Cost | Permanent Unlock | Effect |
|---|---|---|
| **100 CS** | Backpack +1 cell | Starting backpack 3×3 → 3×4 (12 cells вместо 9) |
| **250 CS** | Skip Tutorial Biome | Start expedition at Fire Desert (биом 2), не Ice Castle |
| **500 CS** | Custom Starting School | Choice: Red/Blue/Green как primary school с D1 |
| **750 CS** | Carry-over Slot +1 | Базовая carry capacity 3 → 4 артефакта |
| **1000 CS** | Hero Board Perma Slot +1 | 4th perma synergy slot (вместо 3 max) |
| **1500 CS** | Hero Stat Multiplier (+10% HP) | Permanent +10% hero max HP base |
| **2000 CS** | Synergy Stack Auto-Trigger Faster | Synergy procs check каждые 3 sec вместо 5 sec |
| **2500 CS** | Hero Board Perma Slot +2 | 5th perma slot (max custom build complexity) |
| **5000 CS** | New Starting Biome (Cosmic Vault directly) | Skip биомы 1-4, start at Cosmic Vault |
| **10000 CS** | Master Re-spec | Reset all CS purchases, refund 100% |

**Strategy**: First prestige игрок обычно покупает Backpack +1 cell (100 CS) + Custom Starting School (500 CS). Total 600 CS, leaves 200-900 для future.

## 6. What resets vs what persists

### Resets on Prestige (lost)
- Все артефакты в backpack
- Hero level (back to Level 1)
- Hero Board day unlocks state (D5, D10, D14, etc) — re-locked, нужно играть снова
- Current expedition progress (если в середине run)
- Workshop hero stat upgrades (purchased через Soul Points)
- Daily quests progress
- Weekly Challenge ranking

### Persists across Prestige (kept)
- Codex of discovered synergies (всё что игрок видел — сохраняется)
- Cosmic Shards balance + previously purchased permanent unlocks
- Cosmetic items (Battle Pass skins, trail FX)
- Player profile stats (total kills, total runs, etc.)
- Achievements
- Tutorial completion (не нужно проходить снова если был ранее)
- Soul Points balance (carries over, не reset)

## 7. Prestige loop intent — почему это работает

**Day 1 (first time):** Игрок осваивает basic loop, доходит до D14-D21, делает 3-5 expeditions, накапливает 5000 Soul Points.

**First Prestige (~D14-D21):** Получает 800-1200 CS. Тратит на Backpack +1 cell + Custom Starting School. Restart с advantage.

**Day 1 (post-prestige):** Hero starts с 3×4 backpack (12 cells) + chosen school. Прохождение биомов faster, accumulates Soul Points faster, доходит до Day-equivalent unlocks faster.

**Second Prestige (~D7-D14 post-first):** Получает 1500-2500 CS. Тратит на Carry-over +1 + Hero Board perma slot. **Now игрок имеет 5 carry slots, 4 perma slots, 12-cell backpack.**

**By Third Prestige:** Game становится **fundamentally different** — игрок имеет permanent advantages которые были impossible на первой playthrough. Это "endless replayability через permanent reshape rules".

## 8. Anti-fatigue mechanisms

[Чтобы prestige не казался гриндом:]

- **Visible progress**: Cosmic Shards ticker viewable во время expedition (live preview "if you prestige now: ~750 CS")
- **Achievement-style milestones**: каждый 5-й prestige = bonus 500 CS + special cosmetic
- **No paywall**: все CS unlocks доступны free (через play time), $19.99 IAP только accelerates
- **Optional**: prestige не обязателен для progression — игрок может играть без prestige forever (но unlocks gate-кают content novelty)

## 9. Premium IAP integration

**Hero Ascension Fast-Track $12.99 (one-time)** — instant prestige + 500 bonus CS.
**Cosmic Vault Pass $19.99/month** — +30% Soul Points (faster prestige cycle), +10% Cosmic Shards earned (whale acceleration).

⚠️ **No P2W**: все CS unlocks обтекаемы через play time. IAP только сокращает grind, не unlock'ает power inaccessible to free.

## 10. UI: Cosmic Altar Hub menu

```
[Hub] → Cosmic Altar tab
   ├─ Eligibility status (✓ ready / X requirements remaining)
   ├─ Live "if you prestige now" preview (CS earned)
   ├─ Cosmic Shards balance
   ├─ Permanent unlocks shop (10 items)
   ├─ Achievement-style "Prestige Tracker" (count, milestones)
   └─ "Prestige Now" button (если eligible)
```

## 11. Open questions

1. Soul Points conversion rate — 0.001 правильный? Может слишком мало или много?
2. First Prestige timing — на D14-D21 OK, или earlier (D7) для onboarding?
3. Cosmic Shards spending UI — list or grid?
4. Master Re-spec (10000 CS) — full refund OK или partial?
5. Prestige cinematic — 5 sec OK или нужно longer / shorter?
6. Cosmic Vault Pass IAP overlap with Battle Pass?
7. Should Prestige reset Daily Goal progress?
