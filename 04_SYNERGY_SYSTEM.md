# 04 — Synergy System

## 1. Архитектура synergy

**Synergy** — это пассивная способность, активирующаяся автоматически при достижении конкретного количества артефактов определённой школы в backpack. Механика не требует активного выбора игрока — система сама отслеживает условия активации.

**Stack count** определяется количеством артефактов школы в backpack, включая активные slots (3×3 = до 9 ячеек базово, расширяется до 16 в поздней игре после D30).

**Максимальный tier артефакта** теперь **5** (был 4). Synergies tier-5 всё ещё считаются как 5-stack одинаковой школы (5 разных артефактов), не как наличие одного tier-5 артефакта.

**Trigger check** происходит:
- Каждые 5 секунд во время боя (сканирование backpack состояния)
- При размещении артефакта в lobby (instant activation)
- При удалении артефакта из backpack

**Visual feedback:**
- Иконка synergy мигает золотым цветом при активации
- Звуковой сигнал (chime) при первой активации в бою
- Макс 3 активных synergy badges одновременно на экране (если больше — показываются топ по priority)

---

## 1.5. Active abilities vs Passive synergies — две разные системы

ARTIFEX имеет ДВЕ progression layers:

**1. Passive synergies** (этот документ)
   - Trigger: stack count артефактов в backpack (>= threshold)
   - Activation: автоматическая, no player choice
   - Effect: пассивный buff (постоянный или procs at интервалах)
   - Examples: "Crimson Aegis" (10% vampirism, always-on), "Hellfire Storm" (AOE pulse каждый 5-й hit)

**2. Active abilities** (см. `11_ARTIFACTS_CATALOG.md`)
   - Trigger: tap по active slot icon в combat
   - Activation: player input
   - Effect: instant burst (cooldown-based)
   - Examples: "Missile Volley" (tap → 3 projectiles), "Frost Barrier" (tap → +40% HP shield)
   - Каждый артефакт имеет ОДНУ ability — игрок выбирает 3 артефакта в active slots
   
Артефакт в active slot **продолжает контрибутить** в passive synergy stack count.

**Пример комбинации:**
- В backpack: 3× red артефакта (Crimson Core T2, Crimson Core T3, Bloodforge T1)
- 1 из этих 3 в active slot (например Crimson Core T3)
- Passive synergy: "Pyromancer Combo" (3-stack red) — auto-active, AOE on crit
- Active ability в slot: Crimson Core T3 = Missile Volley standard (3 projectiles, 8s CD)
- Игрок получает **обе** benefits.

---

## 2. Синтаксис описания synergy

Каждая synergy описывается в следующем формате:

| Параметр | Описание |
|---|---|
| **Name** | English game name + Russian пояснение |
| **Trigger condition** | Stack count школы + требования к другим школам |
| **Effect** | Конкретные игромеханические изменения (damage, heal, debuff, buff) с числовыми значениями |
| **Cooldown / Proc rate** | Ограничения на частоту срабатывания (если apply) |
| **Visual + Audio cue** | Визуальная и звуковая обратная связь |
| **Discovery moment** | Примерный день игры (day estimate), когда synergy первый раз unlock'ается в типичной прогрессии |

---

## 3. RED SCHOOL synergies (5 базовых)

### 3.1 Tier-1: "Burning Touch" (1× red)

- **Trigger:** 1+ красный артефакт в backpack
- **Effect:** +5% урона всем атакам героя (multiplicative)
- **Cooldown:** Всегда активна, нет cooldown
- **Visual cue:** Красное свечение вокруг героя; оранжевые частицы на каждом hit
- **Audio cue:** Тихий звук горения (ambient loop, не раздражающий)
- **Discovery:** D1 (тривиальный, первый red artifact автоматически активирует)
- **Interaction:** Стакует с другими damage multipliers аддитивно

### 3.2 Tier-2: "Crimson Aegis" (2× red)

- **Trigger:** 2+ красных артефакта в backpack
- **Effect:** 10% vampirism на hit — 10% от нанесённого урона восстанавливает HP героя
- **Cooldown:** Нет, срабатывает на каждый hit
- **Visual cue:** Красный ауральный щит с пульсацией; жизненные шарики летят к герою
- **Audio cue:** Звук восстановления HP (регенерационный звон)
- **Discovery:** D1-D2 (второй red artifact)
- **Scaling:** Vampirism% не масштабируется с hero damage, всегда фиксированный %

### 3.3 Tier-3: "Pyromancer Combo" (3× red)

- **Trigger:** 3+ красных артефакта в backpack
- **Effect:** На каждый crit hit — AOE flame burst (radius 2 tiles, 80% от hero damage всем врагам в радиусе)
- **Cooldown:** Нет, срабатывает на каждый crit
- **Crit chance baseline:** 15% (без других синергий)
- **Visual cue:** Оранжевый взрыв огня на позиции героя; враги получают красную вспышку
- **Audio cue:** Звук взрыва пламени (distinct from regular hits)
- **Discovery:** D2-D3 (третий red artifact)
- **Damage calculation:** AOE damage = (hero damage * 0.8) * all enemy multipliers

### 3.4 Tier-4: "Inferno Chain" (4× red)

- **Trigger:** 4+ красных артефакта в backpack
- **Effect:** Crits chain to next nearest enemy (максимум 3 прыжка), каждый цепной hit наносит -25% damage от предыдущего
  - Первый hit: X damage
  - Второй (chain): X * 0.75 damage
  - Третий (chain): X * 0.75 * 0.75 damage
- **Cooldown:** 1.5 сек между chain triggers (не более одной цепи в 1.5 сек)
- **Visual cue:** Красные молнии между врагами; вторичные враги подсвечиваются для ясности
- **Audio cue:** Звук электрического разряда/цепи
- **Discovery:** D5-D7 (четвёртый red artifact)
- **Interaction:** Chain прыгает на ближайшего врага по дистанции, игнорирует стены/障碍

### 3.5 Tier-5: "Hellfire Storm" (5× red)

- **Trigger:** 5+ красных артефактов в backpack (независимо от их tier)
- **Effect:** Каждый 5-й hit героя генерирует AOE pulse (radius 3 tiles, 150% от hero damage, всем врагам в радиусе)
- **Cooldown:** Max 1 trigger на каждые 5 hits (rate limiting)
- **Stack cap:** Макс 3 одновременных pulse'а (самый старый исчезает)
- **D25+ nerf:** После D25 synergy proc rate снижается на 20% (анти-OP механика)
- **Visual cue:** Полноэкранный жёлто-оранжевый pulse; враги мигают красным
- **Audio cue:** Громкий звук огненной бури + rumble effect (если поддерживается)
- **Discovery:** D8-D12 (пятый red artifact + чистая красная сборка)
- **Damage cap:** Pulse не может быть больше 180% hero damage даже с multipliers
- **Note:** '5× red' = 5 разных red артефактов (regardless of tier). Можно достичь до Tier-5 артефактов в backpack.

---

## 4. BLUE SCHOOL synergies (5 базовых)

### 4.1 Tier-1: "Frosted Skin" (1× blue)

- **Trigger:** 1+ синий артефакт в backpack
- **Effect:** +8% к максимальному HP героя
- **Cooldown:** Всегда активна, пересчитывается при изменении backpack
- **Visual cue:** Голубой ледяной щит; частицы льда вокруг героя
- **Audio cue:** Тихий звук заморозки (ice crystallization ambient)
- **Discovery:** D1 (первый blue artifact)
- **Scaling:** HP увеличивается немедленно при активации

### 4.2 Tier-2: "Glacial Ward" (2× blue)

- **Trigger:** 2+ синих артефакта в backpack
- **Effect:** 12% пассивное снижение получаемого урона (damage reduction shield)
- **Cooldown:** Всегда активна
- **Visual cue:** Прозрачный ледяной щит вокруг героя; враги видят синие брызги на попадании
- **Audio cue:** Звук ледяного щита на hit (crisp, ясный)
- **Discovery:** D1-D2 (второй blue artifact)
- **Interaction:** Damage reduction применяется ДО healing effects (не уменьшает healing)

### 4.3 Tier-3: "Permafrost Pulse" (3× blue)

- **Trigger:** 3+ синих артефакта в backpack
- **Effect:** Каждые 8 секунд герой выпускает heal-pulse (восстанавливает 15% от макс HP, radius 3 tiles, affects all friendlies если есть allies)
- **Cooldown:** Строго каждые 8 сек (не player-triggered)
- **Visual cue:** Голубой импульс от героя; враги видят синий свет, получают -10% attack speed в радиусе на 2 сек
- **Audio cue:** Звон ледяного импульса (bell-like)
- **Discovery:** D2-D3 (третий blue artifact)
- **Stacking:** Если несколько heroes, каждый имеет свой pulse timer

### 4.4 Tier-4: "Frozen Sentinel" (4× blue)

- **Trigger:** 4+ синих артефакта в backpack
- **Effect:** 1 free dodge per wave — автоматически активируется при иначе летальном hit
  - Герой становится невидимым на 0.5 сек
  - Берёт 0 damage
  - Сразу контр-атакует (1 free hit на nearest enemy, 50% hero damage)
- **Cooldown:** 1 dodge per wave (сбрасывается при начале новой волны)
- **Visual cue:** Синяя вспышка; герой на мгновение исчезает и переставляется
- **Audio cue:** Звук ледяного щита (shield break prevention sound)
- **Discovery:** D5-D7 (четвёртый blue artifact)
- **Interaction:** Dodge использует HP автоматически, не требует player input

### 4.5 Tier-5: "Absolute Zero" (5× blue)

- **Trigger:** 5+ синих артефактов в backpack (независимо от их tier)
- **Effect:** Все враги на экране получают:
  - -40% speed движения
  - -25% attack speed
  - Длительность: persistent (пока 5+ blue в backpack)
- **Cooldown:** Нет, пока условие выполняется — эффект активен
- **Visual cue:** Полноэкранный синий оверлей; враги движутся медленнее (видимо замедленные)
- **Audio cue:** Глубокий звук льда, замедленный ambient
- **Discovery:** D8-D12 (пятый blue artifact + чистая синяя сборка)
- **Interaction:** Не стакует с другими slow эффектами, берёт strongest
- **Note:** '5× blue' = 5 разных blue артефактов (regardless of tier). Можно достичь до Tier-5 артефактов в backpack.

---

## 5. GREEN SCHOOL synergies (5 базовых)

### 5.1 Tier-1: "Vital Roots" (1× green)

- **Trigger:** 1+ зелёный артефакт в backpack
- **Effect:** +3% пассивной HP регенерации в секунду (вне боя и в бою)
- **Cooldown:** Всегда активна
- **Visual cue:** Зелёное свечение вокруг героя; маленькие зелёные шарики восстанавливают HP
- **Audio cue:** Звук роста растений (organic, природный)
- **Discovery:** D1 (первый green artifact)
- **Scaling:** Regen% от max HP (не от current), применяется каждый tick

### 5.2 Tier-2: "Venomous Bloom" (2× green)

- **Trigger:** 2+ зелёных артефакта в backpack
- **Effect:** На каждый hit герой наносит poison stack на врага
  - Poison damage: 3 damage per stack per second
  - Max 5 stacks на одного врага
  - Duration: 5 секунд per stack (refresh если новый hit)
- **Cooldown:** Нет, каждый hit добавляет 1 stack (если есть слоты)
- **Visual cue:** Враги получают зелёное свечение (poison indicator); число стеков видно над врагом
- **Audio cue:** Звук отравления (squelch, жидкостный звук)
- **Discovery:** D1-D2 (второй green artifact)
- **Stack cap:** Глобально на одного врага 5 стеков, дополнительные hits не добавляют

### 5.3 Tier-3: "Plague Doctor" (3× green)

- **Trigger:** 3+ зелёных артефакта в backpack
- **Effect:** На смерть врага — poison spreads на 2 ближайших живых врагов (1 stack на каждого)
- **Cooldown:** Каждая смерть триггерит эффект
- **Visual cue:** Зелёное облако распространяется от умершего врага; целевые враги мигают зелёным
- **Audio cue:** Звук распространения газа (whoosh)
- **Discovery:** D2-D3 (третий green artifact)
- **Interaction:** Если 2 ближайших врага уже имеют max stacks (5), effect не применяется

### 5.4 Tier-4: "Lifethorn" (4× green)

- **Trigger:** 4+ зелёных артефакта в backpack
- **Effect:** 5% от всего урона ядом (poison damage) который герой наносит = восстанавливается как HP
  - Пример: если враг берёт 100 poison damage, герой восстанавливает 5 HP
- **Cooldown:** Нет, каждый poison tick рассчитывает healing
- **Visual cue:** Зелёные энергетические волны от врагов к герою
- **Audio cue:** Звук поглощения энергии (absorb sound)
- **Discovery:** D5-D7 (четвёртый green artifact)
- **Stacking:** Суммирует со всеми poison sources (включая Plague Doctor spreads)

### 5.5 Tier-5: "Nature's Embrace" (5× green)

- **Trigger:** 5+ зелёных артефактов в backpack (независимо от их tier)
- **Effect:** Каждые 3 секунды герой может активировать полную очистку poison stacks на одного врага. За каждый очищенный stack герой восстанавливает 20% max HP (максимум 100% за 5 stacks)
  - Пример: враг с 3 stacks = hero восстанавливает 60% max HP
- **Cooldown:** Один full clear per 3 sec
- **Visual cue:** Враг становится зелёным; heal burst от героя
- **Audio cue:** Звон исцеления (healing chime)
- **Discovery:** D8-D12 (пятый green artifact + чистая зелёная сборка)
- **Interaction:** Выбор врага (nearest или с most stacks) автоматический
- **Note:** '5× green' = 5 разных green артефактов (regardless of tier). Можно достичь до Tier-5 артефактов в backpack.

---

## 6. CROSS-SCHOOL SYNERGIES (4 базовых комбо)

### 6.1 "Frostforge" (3× red + 2× blue)

- **Trigger:** 3+ красных И 2+ синих артефактов одновременно в backpack
- **Effect:**
  - +30% урона на все атаки
  - 15% chance на freeze при каждом hit (freeze duration 1.5 сек)
- **Cooldown:** Freeze имеет cooldown 0.5 сек между application'ами на одного врага
- **Visual cue:** Красно-синие брызги; враги получают ледяную корону
- **Audio cue:** Звук горячего льда (противоречивый, интересный)
- **Discovery:** D7-D10 (комбинация 3R+2B требует прогресса в обоих школах)
- **Interaction:** Freeze переопределяет медленный эффект, freeze는 полная остановка

### 6.2 "Pandemic Blaze" (3× red + 2× green)

- **Trigger:** 3+ красных И 2+ зелёных артефактов одновременно в backpack
- **Effect:**
  - +25% урона на все атаки
  - На каждый hit poison spreads на 2 ближайших врагов (1 stack на каждого, independent от Plague Doctor)
- **Cooldown:** Нет, каждый hit применяет spread
- **Visual cue:** Красно-зелёные брызги; враги получают горящий poison (orange-green свечение)
- **Audio cue:** Звук огня + газа (hybrid)
- **Discovery:** D7-D10 (комбинация 3R+2G)
- **Interaction:** Spreads происходит ДО poison cap check, поэтому может быстро стакировать

### 6.3 "Tidelock" (3× blue + 2× green)

- **Trigger:** 3+ синих И 2+ зелёных артефактов одновременно в backpack
- **Effect:**
  - -20% enemy movement speed на hit
  - +10% healing от слоу'd врагов (каждый hit на slowed врага восстанавливает 10% урона как HP)
- **Cooldown:** Slow длится 3 сек, healing применяется каждый hit
- **Visual cue:** Синее-зелёное вихревое облако вокруг героя
- **Audio cue:** Звук воды + жидкости
- **Discovery:** D8-D12 (комбинация 3B+2G)
- **Interaction:** Healing от slow зависит от hero damage, не от slow amount

### 6.4 "Prismatic Shield" (2× red + 2× blue + 2× green)

- **Trigger:** 2+ каждой школы одновременно (minimum 6 артефактов, balanced approach)
- **Effect:**
  - +15% урона на все атаки
  - +10% снижение получаемого урона
  - +5% пассивной HP регенерации в сек
- **Cooldown:** Все эффекты всегда активны при условии
- **Visual cue:** Радужный щит вокруг героя (RGB слои)
- **Audio cue:** Гармоничный звон (три тона)
- **Discovery:** D10-D14 (требует балансированной multi-school сборки)
- **Interaction:** Это самая "мирная" синергия, не имеет агрессивных оффенсивных эффектов

---

## 7. TIER-4 CROSS-SCHOOL SYNERGIES (5 редких комбо)

### 7.1 "Prismatic Burst" (R + B + G в active slot rotation)

- **Trigger:** Active slot 1, 2, 3 содержат по одному артефакту разной школы (R+B+G) И условие выполняется на протяжении 2+ волн подряд (waves 6-7-8 sequence)
- **Effect:**
  - Hit chain на 3 ближайших врагов
  - +30% урона на каждый chain hit
  - Apply slow debuff на каждого враага в цепи (1 сек duration)
- **Cooldown:** 1 сек между chain triggers
- **Visual cue:** Радужная цепь между врагами
- **Audio cue:** Звук магии (ethereal, пространственный)
- **Discovery:** D16-D18, wave 15+ encounters (требует длительного поддержания сборки и специфичного слот-порядка)
- **Mechanism:** Очень редко активируется, требует точного управления backpack'ом и выживания через waves

### 7.2 "Void Anchor" (B + G + R специфичная sequence)

- **Trigger:** Slot 1 содержит green, slot 2 содержит blue, slot 3 содержит red — РОВНО этот порядок, никакой flexibility
- **Effect:**
  - Pull 1 врага из-за края экрана обратно в коридор
  - 3 сек stun на pulled врага
  - Follow-up hit наносит ×2 damage (double damage)
- **Cooldown:** 1 pull per wave (может быть использовано только раз за волну)
- **Visual cue:** Чёрное-фиолетовое вихревое облако; враг телепортируется
- **Audio cue:** Звук разрыва пространства (void rupture)
- **Discovery:** D18-D20 (после второго биома cleared, требует точной слот-организации)
- **Interaction:** Очень мощно против edge-casting врагов, но требует точного лотери slots

### 7.3 "Inferno Cascade" (2× red + 1× blue в active slots)

- **Trigger:** 2 красных артефакта в любых slots + 1 синий в slot 3 (левый bottom)
- **Effect:**
  - На каждый красный hit — explosion на ближайшего врага (60% hero damage)
  - Stacking burn debuff на врага (3 stacks = detonate на 200% damage burst)
  - Burn duration: 4 сек, max 3 stacks per enemy
- **Cooldown:** 0.5 сек между explosions (не может быть более 1 explosion per 0.5s)
- **Visual cue:** Красно-оранжевые explosion cascades; враг горит (visual burn indicator)
- **Audio cue:** Серия взрывов (cascade sound design)
- **Discovery:** D20-D22, wave 22+ chain proc encounters (requires достаточное количество damage для cascade)
- **Mechanism:** Complex proc system, требует понимания burn stack mechanics

### 7.4 "Tideshift" (4-chain: G + R + G + B в backpack adjacency)

- **Trigger:** Specific 4-artifact sequence в backpack adjacency — например, 4 артефакта подряд в одном ряду (row of 4): Green, Red, Green, Blue
- **Effect:**
  - Swap player position с enemy (телепорт позади врага)
  - Follow-up hit наносит +50% damage (повышенный урон на repositioned hit)
  - Enemy frozen на следующую волну (-50% attack speed за первые 3 сек новой волны)
- **Cooldown:** Max 1 trigger per combat cycle (3% chance proc per hit, очень редко)
- **Visual cue:** Синие портальные спирали; позиции меняются со звуком
- **Audio cue:** Звук телепортации (portal whoosh)
- **Discovery:** D23-D25 (очень редко, требует экспериментирования со slots и lucky encounter)
- **Mechanism:** Самая сложная синергия по активации, требует precise backpack management и внимание к adjacency patterns

### 7.5 "Chronofracture" (3+ blue pure школа)

- **Trigger:** 3+ синих в backpack, 0 красных, 0 зелёных (pure blue mono-build)
- **Effect:**
  - Каждый 3-й синий hit героя — reset dodge cooldown
  - +15% attack speed на 5 сек (stacking, может быть до 3 stacks одновременно)
  - На 5-й hit подряд (в рамках этого combo) добавляется 1 free dodge counter
  - -15% active ability cooldown for 5s (каждый 3rd blue hit)
- **Cooldown:** Stacking attack speed имеет 1.5 сек decay между stacks
- **Visual cue:** Синие вихревые частицы; герой движется быстро (видимо ускоренный)
- **Audio cue:** Звук времени (time manipulation, стремительный)
- **Discovery:** D24+ (requires dedication к single-school blue build и высокий skill level)
- **Scaling:** Attack speed bonus не cap'лася при множественных Chronofracture procs

---

## 8. SYNERGY INTERACTION RULES

### Синергия между синергиями

**Стакование:** Все синергии стакуются **аддитивно** при совместимых эффектах:
- Damage multipliers: (1 + 0.05) × (1 + 0.10) × (1 + 0.15) = 1.32975 (multiplicative всё же)
- Healing bonuses: 10% + 5% + 3% = 18% (additive)
- Speed bonuses: +15% + +10% = +25% attack speed (additive)

**Конфликтующие эффекты:** Если две синергии предоставляют один и тот же stat (e.g., +10% damage и +8% damage):
- Самый сильный эффект WIN'ит
- Слабый отключается (не стакуется)

**Cross-school overwrite:** Cross-school синергии переопределяют single-school синергии того же типа эффекта:
- Пример: Burning Touch (5% damage)被 Frostforge (30% damage) overwritten
- Более сильный всегда берётся

**Tier hierarchy:** Tier-4 синергии переопределяют Tier-3 синергии того же цветового школы:
- Inferno Chain (4×red) overwrite Pyromancer Combo (3×red) if both trigger
- Но это зависит от mechanic (e.g., если Pyromancer Combo is AOE а Inferno Chain is chain, они могут оба быть активны)

**Визуальный лимит:** Максимум 3 synergy badges одновременно на экране:
- Показываются top-3 по damage priority
- Остальные скрыты в Codex (не забыт, просто не показывается)

---

## 8.5. Synergy + Active ability interaction

### Buff stacking

Когда player активирует Active ability — passive synergies продолжают работать. Effects стакуются:

**Пример:**
- Passive: "Crimson Aegis" (2-stack red) = 10% vampirism
- Active: tap Bloodforge T3 = "Berserk" (+80% damage 3s, -10% HP)
- Combined: hero deals +80% damage AND восстанавливает 10% from each hit
- 10% vampirism × 80% damage = 8% effective HP recovery per damage dealt during berserk

### Cooldown interaction

Active abilities имеют собственный cooldown (8-25s). Passive synergies не влияют на active CD напрямую, но некоторые synergies могут:
- "Chronofracture" tier-4 blue mono-build: каждый 3rd blue hit hero — reset dodge cooldown AND -15% active ability cooldown for 5s

### Mutual exclusion

Некоторые active abilities + passive synergies overlap (например "Frost Barrier" active даёт shield, "Glacial Ward" passive даёт damage reduction). В этом случае:
- Effects стакуются additively (10% DR + 40% shield = both apply)
- НЕТ mutual exclusion правил, кроме explicitly stated в artifact catalog

---

## 9. SYNERGY DISCOVERY & CODEX

### Как игрок узнаёт о синергиях

**First activation:**
- Pop-up уведомление: "NEW SYNERGY UNLOCKED: [name]"
- Короткая animation (синергия мигает на экране)
- Codex автоматически обновляется

**Codex menu в Hub:**
- Доступен в главном меню
- Показывает все discovered синергии с полными descriptions
- Заблокированные синергии отображаются как silhouette + hint ("Unlock by stacking 4× Red artifacts")

**Progression visualization:**
- Каждая синергия имеет visual progress bar (e.g., "2/4 Red artifacts needed")
- Когда player близко к unlock'у (2 из 4), Codex highlights hint

**Total count:**
- 5 Red single-school
- 5 Blue single-school
- 5 Green single-school
- 4 basic cross-school (Frostforge, Pandemic Blaze, Tidelock, Prismatic Shield)
- 5 Tier-4 rare cross-school (Prismatic Burst, Void Anchor, Inferno Cascade, Tideshift, Chronofracture)
- **Total: 24 синергии**

---

## 10. SYNERGY BALANCE CONSTRAINTS (Anti-OP механики)

| Проблема | Решение | Обоснование |
|---|---|---|
| Hellfire Storm (5× red) OP damage | Max 1 trigger per 5 hits, max 3 stacks одновременно | Rate-limiting предотвращает spam damage |
| Cross-school multipliers stack бесконечно | Hard cap 2.5× total damage multiplier (все synergies вместе) | Prevents exponential scaling |
| Pure школа dominance D25+ | Enemy school resistances -20% single-school damage (resistance scaling) | Late-game balance: specialized builds ослабляются |
| Synergy proc spam overwhelming | Visual feedback только top-3 synergies на экране | Cognitive load management |
| Tier-4 cross combos OP | 1.5-3s cooldowns на Tier-4 effects | Frequency capping |
| Pure blue (Absolute Zero) breaking game | Non-stacking multiplicative cap на slow (max -50% движение) | Speed не может идти ниже 50% normal |
| Tier-5 single-school abuse | Damage cap на pulse/burst effects (150% hero damage max) | Explosion damage capped |

### Late-game scaling (D25+)

После D25 вводятся enemy resistances:
- **Red resistance:** -20% к single-school red synergy damage (красный урон слабеет)
- **Blue resistance:** -20% к single-school blue synergy slow effectiveness (синий slow слабеет)
- **Green resistance:** -20% к single-school green poison damage (зелёный poison слабеет)

Это позволяет игрокам с diversified builds (cross-school) оставаться конкурентоспособными против pure builds.

---

## 11. IMPLEMENTATION PRIORITY

**0. Read 11_ARTIFACTS_CATALOG.md first** — active abilities depend on artifact-specific implementations

**Phase 1 (Prototype, Biome 1):** Основные механики
1. Red Tier-1 to Tier-3 (Burning Touch, Crimson Aegis, Pyromancer Combo)
2. Blue Tier-1 to Tier-3 (Frosted Skin, Glacial Ward, Permafrost Pulse)
3. Green Tier-1 to Tier-3 (Vital Roots, Venomous Bloom, Plague Doctor)
4. Codex system (discovery, unlock hints)
5. Trigger check logic (every 5s scan, trigger detection)

**Phase 2 (Vertical slice, Biome 1-2):**
6. Red Tier-4 (Inferno Chain)
7. Blue Tier-4 (Frozen Sentinel)
8. Green Tier-4 (Lifethorn)
9. Cross-school basic (Frostforge, Pandemic Blaze, Tidelock)
10. Synergy interaction rules (stacking, overwrites)

**Phase 3 (Content, Biome 2-3):**
11. Prismatic Shield (balanced cross-school)
12. Red Tier-5 (Hellfire Storm) с rate-limiting
13. Blue Tier-5 (Absolute Zero)
14. Green Tier-5 (Nature's Embrace)

**Phase 4 (Polish, Biome 4-5):**
15. Tier-4 cross combos (Prismatic Burst, Void Anchor, Inferno Cascade, Tideshift, Chronofracture)
16. Late-game balancing (D25+ resistances)
17. VFX/SFX polish на все синергии
18. Codex UI refinement

---

## 12. OPEN QUESTIONS & DECISIONS

### 1. Stack Counting Mechanism
**Question:** Tier-weighted (1× tier-3 artifact = 3 stacks) или per-piece (1 artifact = 1 stack regardless of tier)?

**Current spec:** Per-piece (1 artifact = 1 stack). Это проще для реализации и понимания игроком.

**Alternative:** Tier-weighted потребует более сложную UI (displaying weighted stacks) и может confuse игроков.

### 2. Cross-school Requirements
**Question:** Simultaneous (3R + 2B должны быть одновременно в backpack в момент проверки) или sequential (может быть активированы через разные runs)?

**Current spec:** Simultaneous (они должны быть в backpack в момент trigger check). Более интересный геймплей и требует активного decision-making.

### 3. Synergy Proc Visualization
**Question:** Full-screen flash (весь экран мигает при activation) или только на enemy (small indicator на враге)?

**Current spec:** Комбо:
- Иконка synergy мигает золотым в UI
- Звуковой cue (chime)
- Визуальный эффект на enemies (зависит от synergy типа)
- Max 3 badges на экране (не overwhelming)

### 4. Synergy Cooldowns
**Question:** Shared global cooldown (все synergies имеют общий cooldown) или per-synergy (каждая свой)?

**Current spec:** Per-synergy (каждая имеет свой cooldown если применимо). Это дает больше flexibility и depth механики.

### 5. Codex Unlock
**Question:** Instant on first trigger или wait until end of run?

**Current spec:** Instant pop-up при первом trigger'е (быстрая feedback). В конце run также отображается в summary.

### 6. Tier-4 Cross Combos Discovery
**Question:** Hint в UI ("Unlock by stacking specific sequence") или pure blind discovery (игрок экспериментирует)?

**Current spec:** Blind discovery для редких Tier-4 combos. Подсказка может быть добавлена в Codex после первого unlock'а для hint на других (e.g., "Similar patterns might exist").

### 7. Synergy Stat Priority (UI Display)
**Question:** Какие synergies показывать топ-3 если больше чем 3 active?

**Current spec:** Priority по damage contribution:
1. Highest damage multiplier synergies first
2. Then control effects (freeze, slow)
3. Then healing synergies

### 8. Backpack Expansion & Synergy Scaling
**Question:** Как synergies скейлятся когда backpack расширяется (до 16 slots)?

**Current spec:** Stack count increases (e.g., 5+ red требует 5, но с 16 slots можно иметь 6-7 red), но Tier-5 requirements остаются same (5+). Для позднейших (новых) Tier-5+ синергий можно вводить 6+ requirements.

---

## 13. TECHNICAL IMPLEMENTATION NOTES

### Synergy Engine Architecture

**Trigger Check Component:**
- Запускается каждые 5 сек в бою (или на event placement в lobby)
- Сканирует backpack состояние
- Вычисляет active synergy list базируясь на текущем content
- Сравнивает с previous frame list (определяет new/removed synergies)
- На change: вызывает OnSynergyActivated/OnSynergyDeactivated events

**Synergy Database:**
- JSON или ScriptableObject с синергией configs
- Fields: ID, name, trigger_condition, effects, cooldown, vfx, sfx, discovery_day
- Каждая синергия = отдельный object с Apply() method

**Interaction Manager:**
- Отслеживает conflicting effects
- Handles overwrite logic (tier-based, strength-based)
- Applies hard caps (2.5× damage multiplier cap)

**Codex Storage:**
- PlayerPrefs или SaveData с discovered synergies list
- On discover: добавляется synergy ID в list
- On load: Codex UI populates с discovered items

### Performance Considerations

- Trigger check 5s interval оптимален (не слишком частый scan)
- Backpack scan O(n) где n=количество artifacts (max 16) — trivial
- Effect application на enemies: batch apply for efficiency
- VFX pooling для common effects (explosions, heal pulses)

---

## 14. METRICS & TELEMETRY

Для balance tracking:

| Metric | Importance |
|---|---|
| Synergy activation frequency | Track which synergies are most used |
| Average synergy count per playthrough | Balance accessibility |
| D25+ survival rate by synergy type | Late-game balance |
| Build diversity (pure vs cross-school %) | Encourage experimentation |
| Average time-to-unlock per synergy | Pacing validation |
