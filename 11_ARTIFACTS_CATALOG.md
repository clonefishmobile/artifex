# 11 — Artifacts Catalog

## 1. Concept

Каждый артефакт в ARTIFEX — это **уникальная боевая сущность** с собственной активной способностью. Игрок собирает артефакты на каждом run, мержит их до высоких тиров, выбирает до 3 в активные слоты для использования в боях.

Архитектура:
- **3 основные школы**: Red (damage/aggression), Blue (defense/control), Green (sustain/poison)
- **Cross-school гибриды**: комбинируют 2-3 школы, доступны в T4-T5
- **15+ уникальных артефактов** для launch (5 на школу minimum)
- **5 легендарных cross-school** артефактов для endgame
- **Expandable** система — посредством добавления новых школ или гибридов post-release

Каждый артефакт содержит:
1. **Имя** (English, evocative)
2. **Школа** + базовый тир дропа (T1, T2, T3)
3. **Уникальная активная способность** — мгновенный эффект на tap, с cooldown
4. **Таблица масштабирования по тирам** (T1 / T2 / T3 / T4 / T5)
5. **Визуальное описание** (sprite/icon)
6. **Лор-флавор** (1-2 предложения)
7. **Биом дропа** (где обычно спавнится)

---

## 2. Active Ability Mechanics

**Как работает активная способность:**

1. Игрок выбирает до 3 артефактов в активные слоты (top-left, top-right, bottom-right экрана)
2. Каждый слот отображает **icon артефакта**
3. Player **тапает по icon** → мгновенно активируется способность
4. На icon появляется **cooldown ring** (визуальный таймер)
5. После завершения cooldown кольцо исчезает, способность готова к использованию

**Тир влияет на:**
- **Эффект силы** (урон, длительность, область, количество вызовов)
- **Cooldown** (чем выше тир, тем ниже CD)
- **T1** = 50% от стандартного эффекта
- **T3** = 100% стандартный (baseline reference)
- **T5** = 200% эффект + значительное снижение cooldown

**Важно:** Активные способности ≠ пассивные синергии. Синергии срабатывают автоматически при нужном количестве артефактов одной школы. Активные способности требуют явного действия игрока.

---

## 3. Red School Artifacts (Damage / Burn / Aggression)

### 3.1 Crimson Core

**Base School:** Red  
**Base Tier:** T1 (стартовый, универсальный дроп)  
**Drop Biomes:** Ice Castle, Fire Desert (приоритет — начальные биомы)  

**Active Ability:** Missile Volley  
*Игрок активирует — 2-5 снарядов летят вперёд по арке, поражая первых встреченных врагов. Каждый снаряд наносит урон, зависящий от тира.*

**Tier Scaling:**
| Тир | Снарядов | Урон (×базовый) | Cooldown |
|---|---|---|---|
| T1 | 2 | 1.25× | 10 sec |
| T2 | 2 | 1.75× | 9 sec |
| T3 | 3 | 2.5× | 8 sec |
| T4 | 4 | 3.25× | 7 sec |
| T5 | 5 | 4.0× + 25% crit chance | 6 sec |

**Visual:** Глowing красный шар с исходящими лучами энергии, пульсирует в такт.  
**Lore:** "Сердце древнего огнетворца — пульсирует жаром боевого духа, дающим силу атаке."  
**Synergy (passive):** 3+ Red artifacts → +15% damage всем Red ability.

---

### 3.2 Pyrohelm

**Base School:** Red  
**Base Tier:** T1  
**Drop Biomes:** Fire Desert, Ancient Forest (hot zones)  

**Active Ability:** Fire Wall  
*Создаёт стену огня перед героем — она существует несколько секунд, блокирует входящие враги-снаряды и наносит урон врагам, которые её касаются.*

**Tier Scaling:**
| Тир | Длительность | Урон/сек | Cooldown |
|---|---|---|---|
| T1 | 2 sec | 0.8× | 12 sec |
| T2 | 2.5 sec | 1.2× | 11 sec |
| T3 | 4 sec | 2× | 10 sec |
| T4 | 4.5 sec | 2.8× | 9 sec |
| T5 | 5 sec | 3.5× + projectile immunity | 8 sec |

**Visual:** Линия оранжево-жёлтого пламени перед героем, волнится и потрескивает.  
**Lore:** "Клейм боевого шлема давних ратников — вызывает огненную завесу защиты."  
**Synergy (passive):** 3+ Red artifacts → Wall duration +0.5 sec.

---

### 3.3 Bloodforge

**Base School:** Red  
**Base Tier:** T2  
**Drop Biomes:** Storm Peak, Ancient Forest (deep zones)  

**Active Ability:** Berserk Surge  
*Герой входит в берсерк-режим — наносит на 80% больше урона собственными атаками, но теряет 10% максимального HP в сек. Эффект длится несколько секунд.*

**Tier Scaling:**
| Тир | Урон бонус | HP затрата/сек | Длительность | Cooldown |
|---|---|---|---|---|
| T2 | +60% | 15% | 3 sec | 15 sec |
| T3 | +80% | 10% | 4 sec | 12 sec |
| T4 | +100% | 8% | 5 sec | 10 sec |
| T5 | +120% | 5% + life steal 30% | 6 sec | 8 sec |

**Visual:** Красная аура вокруг персонажа, кровавые потёки по экрану.  
**Lore:** "Молот древних кузнецов боли — преобразует кровь в ярость, а ярость в смерть врагов."  
**Synergy (passive):** 3+ Red artifacts → Life steal стаёт 20% урона от Berserk attacks.

---

### 3.4 Stormcaller

**Base School:** Red  
**Base Tier:** T3  
**Drop Biomes:** Storm Peak, Cosmic Vault (rare)  

**Active Ability:** Chain Lightning  
*Запускает первый молниевый снаряд по ближайшему врагу, затем цепь молний прыгает между врагами в области, каждый раз наносит урон.*

**Tier Scaling:**
| Тир | Макс. прыжков | Урон/прыжок | Область прыжка | Cooldown |
|---|---|---|---|---|
| T3 | 5 | 2.0× | 6m | 10 sec |
| T4 | 7 | 2.5× | 8m | 8 sec |
| T5 | 10 | 3.2× + stun 1 sec | 10m | 6 sec |

**Visual:** Жёлтые молниевые линии между врагами, электрические разряды.  
**Lore:** "Древний амулет громовержца — притягивает небесные удары на врагов."  
**Synergy (passive):** 3+ Red artifacts → +1 дополнительный прыжок.

---

### 3.5 Inferno Sigil

**Base School:** Red  
**Base Tier:** T4  
**Drop Biomes:** Cosmic Vault, boss drops  

**Active Ability:** Meteor Strike  
*После небольшой задержки огромный метеор падает в центр экрана, поражая большую область взрывом огня и наносящий мощный урон.*

**Tier Scaling:**
| Тир | Урон (×базовый) | Радиус области | Задержка | Cooldown |
|---|---|---|---|---|
| T4 | 5.0× | 8m | 2.5 sec | 18 sec |
| T5 | 7.0× + burn DoT 3 sec | 10m | 2 sec | 15 sec |

**Visual:** Оранжево-красный метеор с огненным хвостом, взрыв при приземлении.  
**Lore:** "Печать небесного огня — вызывает гнев древних звёзд на поле боя."  
**Synergy (passive):** 5+ Red artifacts → Meteor split на 2 smaller meteors на выходе.

---

## 4. Blue School Artifacts (Defense / Freeze / Control)

### 4.1 Frostbite Sigil

**Base School:** Blue  
**Base Tier:** T1  
**Drop Biomes:** Ice Castle, Storm Peak  

**Active Ability:** Frost Barrier  
*Создаёт ледяной щит вокруг героя — поглощает входящий урон. Если враг коснётся щита, он замерзает на короткое время.*

**Tier Scaling:**
| Тир | Шит HP (% макс) | Длительность | Freeze длительность | Cooldown |
|---|---|---|---|---|
| T1 | 30% | 4 sec | 0.5 sec | 12 sec |
| T2 | 35% | 4.5 sec | 1 sec | 11 sec |
| T3 | 40% | 5 sec | 1.5 sec | 10 sec |
| T4 | 45% | 6 sec | 2 sec | 9 sec |
| T5 | 50% + reflect 20% damage | 7 sec | 2.5 sec | 8 sec |

**Visual:** Ледяная оболочка вокруг персонажа с кристаллическими структурами.  
**Lore:** "Знак морозного старца — призывает холод для защиты от врагов."  
**Synergy (passive):** 3+ Blue artifacts → Shield regenerate 5% в сек, если не получена урон.

---

### 4.2 Glacial Hourglass

**Base School:** Blue  
**Base Tier:** T2  
**Drop Biomes:** Ice Castle, Ancient Forest  

**Active Ability:** Temporal Freeze  
*Замораживает всех видимых врагов на несколько секунд — они не могут двигаться или атаковать, но остаются уязвимы для урона.*

**Tier Scaling:**
| Тир | Freeze длительность | Эффект урона к замороженным | Cooldown |
|---|---|---|---|
| T2 | 1.5 sec | нет | 16 sec |
| T3 | 2 sec | +25% damage taken | 14 sec |
| T4 | 2.5 sec | +40% damage taken | 12 sec |
| T5 | 3 sec | +60% damage + shatter на смерти | 10 sec |

**Visual:** Все враги на экране покрываются ледяной коркой, замедленное движение.  
**Lore:** "Часы вечного льда — останавливают время для врагов, но не для героя."  
**Synergy (passive):** 3+ Blue artifacts → Freeze область расширяется на 2m.

---

### 4.3 Permafrost Cube

**Base School:** Blue  
**Base Tier:** T1  
**Drop Biomes:** Ice Castle, Cosmic Vault  

**Active Ability:** Ice Spike  
*Запускает острый кристалл льда в ближайшего врага, пронизывая его и замораживая на месте. Можно поразить несколько врагов на линии.*

**Tier Scaling:**
| Тир | Урон (×базовый) | Freeze длительность | Пронизывающие враги | Cooldown |
|---|---|---|---|---|
| T1 | 1.5× | 1 sec | 1 | 10 sec |
| T2 | 2.0× | 1.25 sec | 2 | 9 sec |
| T3 | 2.5× | 1.5 sec | 3 | 8 sec |
| T4 | 3.0× | 2 sec | 4 | 7 sec |
| T5 | 3.5× + shatter AoE | 2.5 sec | 5 + chain | 6 sec |

**Visual:** Ледяной копьё с заострённым кончиком, оставляет ледяной след.  
**Lore:** "Кубик вечной зимы — ледяное оружие древних фростветра."  
**Synergy (passive):** 3+ Blue artifacts → Spike can chain на соседних врагов.

---

### 4.4 Aegis Wreath

**Base School:** Blue  
**Base Tier:** T3  
**Drop Biomes:** Storm Peak, Cosmic Vault (rare)  

**Active Ability:** Sanctuary  
*Герой активирует полную защиту — моментально исцеляет все HP и становится неуязвимым на несколько секунд.*

**Tier Scaling:**
| Тир | Исцеление | Неуязвимость длительность | Cooldown |
|---|---|---|---|
| T3 | 100% | 3 sec | 25 sec |
| T4 | 100% | 3.5 sec + shield 20% | 20 sec |
| T5 | 100% | 4 sec + shield 40% + allies 50% heal | 18 sec |

**Visual:** Золотистое сияние вокруг героя, защитный купол.  
**Lore:** "Венец святилища — дарует полное спасение в момент отчаяния."  
**Synergy (passive):** 3+ Blue artifacts → Sanctuary также исцеляет проклятия/debuffs.

---

### 4.5 Tideforge Crown

**Base School:** Blue  
**Base Tier:** T3  
**Drop Biomes:** Ancient Forest, Cosmic Vault (rare)  

**Active Ability:** Tidal Wave  
*Огромная волна воды движется вперёд, отбрасывая всех врагов в стороны и оглушая их на место.*

**Tier Scaling:**
| Тир | Knockback сила | Stun длительность | Волны | Cooldown |
|---|---|---|---|---|
| T3 | 4m | 1.5 sec | 1 | 14 sec |
| T4 | 5m | 2 sec | 1 | 12 sec |
| T5 | 6m | 2.5 sec + 30% damage multiplier | 2 waves | 10 sec |

**Visual:** Синяя волна воды с пеной, враги летят в разные стороны.  
**Lore:** "Корона морской силы — приказывает приливам подчиняться воле героя."  
**Synergy (passive):** 3+ Blue artifacts → Wave damage increases по количеству frozen врагов.

---

## 5. Green School Artifacts (Sustain / Poison / Regen)

### 5.1 Verdant Spore

**Base School:** Green  
**Base Tier:** T1  
**Drop Biomes:** Ancient Forest, Ice Castle  

**Active Ability:** Plague Swarm  
*Вызывает облако ядовитых спор, которое атакует врагов автоматически, нанося урон и отравляя цели на расстояние.*

**Tier Scaling:**
| Тир | Спор вызвано | Урон/спора | Poison duration | Cooldown |
|---|---|---|---|---|
| T1 | 2 | 0.5× | 4 sec | 12 sec |
| T2 | 2 | 0.75× | 5 sec | 11 sec |
| T3 | 3 | 1.0× | 6 sec | 10 sec |
| T4 | 4 | 1.25× | 7 sec | 8 sec |
| T5 | 5 | 1.5× + poison stacks | 8 sec | 6 sec |

**Visual:** Облако зелёных спор, которые кружат вокруг врагов.  
**Lore:** "Спора древесного чумы — вызывает яд, который медленно ослабляет врагов."  
**Synergy (passive):** 3+ Green artifacts → Poison damage increases на 20% за каждый poison stack.

---

### 5.2 Lifeweave

**Base School:** Green  
**Base Tier:** T2  
**Drop Biomes:** Ancient Forest, Storm Peak  

**Active Ability:** Vital Bloom  
*Герой мгновенно исцеляется и получает регенерацию на несколько секунд. Каждый килл или hit врага продлевает регенерацию.*

**Tier Scaling:**
| Тир | Мгновенное исцеление (%) | Regen/сек | Длительность | Cooldown |
|---|---|---|---|---|
| T2 | 25% | 5% | 3 sec | 14 sec |
| T3 | 30% | 8% | 4 sec | 12 sec |
| T4 | 35% | 10% | 5 sec | 10 sec |
| T5 | 40% + allies heal | 12% + poison immunity | 6 sec | 8 sec |

**Visual:** Зелёный свет с цветочными всходами вокруг героя.  
**Lore:** "Плетение жизни — волшебство древней природы, дающее телу силу расти снова."  
**Synergy (passive):** 3+ Green artifacts → Regen extends на 0.5 sec за каждого убитого врага.

---

### 5.3 Mossheart

**Base School:** Green  
**Base Tier:** T1  
**Drop Biomes:** Ancient Forest, Ice Castle  

**Active Ability:** Root Strike  
*Герой фиксирует врагов на месте корнями, они не могут двигаться. Все враги, поражённые корнями, получают повышенный урон.*

**Tier Scaling:**
| Тир | Враги заморожены | Длительность | Урон бонус к корневым | Cooldown |
|---|---|---|---|---|
| T1 | 2-3 | 2 sec | +15% | 11 sec |
| T2 | 3 | 2.5 sec | +25% | 10 sec |
| T3 | 4 | 3 sec | +35% | 9 sec |
| T4 | 5 | 3.5 sec | +50% | 8 sec |
| T5 | 6 + AoE root | 4 sec | +70% + pulling | 7 sec |

**Visual:** Зелёные корни выбрасываются из земли, обвивают врагов.  
**Lore:** "Сердце мха — древняя связь с землёй, позволяющая остановить врагов движением."  
**Synergy (passive):** 3+ Green artifacts → Rooted враги также теряют 10% атаки.

---

### 5.4 Toxin Vial

**Base School:** Green  
**Base Tier:** T3  
**Drop Biomes:** Ancient Forest, Cosmic Vault (rare)  

**Active Ability:** Toxic Cloud  
*Создаёт облако токсина, которое медленно распространяется. Враги внутри получают стек яда, который усиливается при повторном попадании.*

**Tier Scaling:**
| Тир | Облако радиус | Poison урон/стек | Макс. стеков | Cooldown |
|---|---|---|---|---|
| T3 | 6m | 1.0× | 3 | 13 sec |
| T4 | 7m | 1.3× | 5 | 11 sec |
| T5 | 8m + explode | 1.6× | 8 + cascade | 9 sec |

**Visual:** Фиолетово-зелёное облако, которое кружит и растёт.  
**Lore:** "Сосуд чистого яда — древнейший наркотик боли, что медленно убивает."  
**Synergy (passive):** 5+ Green artifacts → Poison clouds merge на одно большое облако.

---

### 5.5 Nature's Embrace

**Base School:** Green  
**Base Tier:** T3  
**Drop Biomes:** Ancient Forest, Cosmic Vault (rare)  

**Active Ability:** Sanctum Grove  
*Вызывает священный лес вокруг героя — он исцеляет себя и союзников, и все существа внутри получают урон бонус.*

**Tier Scaling:**
| Тир | Область | Исцеление/сек | Урон бонус | Длительность | Cooldown |
|---|---|---|---|---|---|
| T3 | 6m | 10% HP | +20% | 5 sec | 15 sec |
| T4 | 7m | 12% HP | +30% | 6 sec | 12 sec |
| T5 | 8m | 15% HP + poison immunity | +40% + reflect | 7 sec | 10 sec |

**Visual:** Зелёный лес с деревьями вокруг, светящиеся листья.  
**Lore:** "Объятие природы — древнее святилище, где жизнь плодится и враги слабеют."  
**Synergy (passive):** 3+ Green artifacts → Grove also purge 1 debuff в сек.

---

## 6. Cross-School Legendary Artifacts (T4-T5)

### 6.1 Frostforge Hammer (Red + Blue, T4-T5)

**Active School Mix:** Red (damage) + Blue (control)  
**Drop Biomes:** Storm Peak (T4), Cosmic Vault (T5)  
**Base Tier:** T4  

**Active Ability:** Frost-Forged Strike  
*Герой наносит мощный единичный удар, который замораживает цель и наносит взрывной урон. Frozen враги получают 50% больше урона от других источников.*

**Tier Scaling:**
| Тир | Урон (×базовый) | Freeze длительность | Урон усиление к frozen | Cooldown |
|---|---|---|---|---|
| T4 | 5.0× | 1.5 sec | +30% | 13 sec |
| T5 | 6.5× + chains | 2 sec | +50% | 11 sec |

**Visual:** Молот с ледяной головкой и огненной рукояткой, при ударе создаёт ледяной взрыв.  
**Lore:** "Молот кузницы мороза — союз огня и льда, создающий оружие судьбы."  
**Synergy (passive):** 3+ Red + 3+ Blue → +20% урон от обеих школ.

---

### 6.2 Pandemic Bloom (Red + Green, T4-T5)

**Active School Mix:** Red (damage) + Green (poison)  
**Drop Biomes:** Ancient Forest (T4), Cosmic Vault (T5)  
**Base Tier:** T4  

**Active Ability:** Burning Plague  
*Вызывает волну огненного яда, который наносит урон и отравляет врагов. Отравленные враги при смерти взрываются, распространяя poison на соседей.*

**Tier Scaling:**
| Тир | Волн урон | Poison duration | Взрыв радиус | Cooldown |
|---|---|---|---|---|
| T4 | 3.5× | 5 sec | 4m | 14 sec |
| T5 | 4.5× + burn DoT | 7 sec | 6m + cascade | 11 sec |

**Visual:** Красно-зелёная волна с огнём и спорами, враги покрываются горящей чумой.  
**Lore:** "Расцвет чумы — объединение огня боли и яда смерти."  
**Synergy (passive):** 3+ Red + 3+ Green → Poison взрывы имеют +40% радиус.

---

### 6.3 Tideroot Sceptre (Blue + Green, T4-T5)

**Active School Mix:** Blue (control) + Green (sustain)  
**Drop Biomes:** Storm Peak (T4), Cosmic Vault (T5)  
**Base Tier:** T4  

**Active Ability:** Healing Tide  
*Создаёт волну исцеляющей воды, которая движется вперёд, исцеляя героя и замораживая врагов на пути. Враги, получившие исцеление отраженное, становятся слабее.*

**Tier Scaling:**
| Тир | Волн исцеление (%) | Freeze длит | Ослабление врагов | Cooldown |
|---|---|---|---|---|
| T4 | 25% | 1.5 sec | -20% атаки | 13 sec |
| T5 | 35% + allies heal | 2 sec | -30% атаки + regen | 10 sec |

**Visual:** Волна голубо-зелёной воды с целебными вихрями.  
**Lore:** "Скипетр корневых приливов — дарует исцеление через холод древних морей."  
**Synergy (passive):** 3+ Blue + 3+ Green → Wave velocity удваивается.

---

### 6.4 Prism Crystal (Red + Blue + Green, T5 only)

**Active School Mix:** All three (R+B+G)  
**Drop Biomes:** Cosmic Vault (boss drops, очень редко)  
**Base Tier:** T5 only  

**Active Ability:** Prismatic Burst  
*Эксплозия призматического света поражает до 5 врагов, каждый получает комбинированный эффект: урон (Red) + freeze (Blue) + poison (Green). Враги, получившие все три эффекта, оглушаются.*

**Tier Scaling:**
| Тир | Макс. цели | Damage × | Эффект сила | Stun если все 3 | Cooldown |
|---|---|---|---|---|---|
| T5 | 5 | 3.5× | 100% | 2 sec | 12 sec |

**Visual:** Радужный взрыв, каждый луч имеет собственный цвет (красный, голубой, зелёный).  
**Lore:** "Кристалл спектра — древнейший артефакт, что объединяет все магии в одну."  
**Synergy (passive):** 5+ of all schools → Burst hits extra 2 targets.

---

### 6.5 Void Anchor (Red + Blue + Green, T5 only)

**Active School Mix:** All three (R+B+G)  
**Drop Biomes:** Cosmic Vault (boss drops, экстра редко)  
**Base Tier:** T5 only  

**Active Ability:** Void Pull  
*Герой тянет одного врага с экрана в центр боевой области, оглушает его на месте, затем два раза наносит по нему взрывной удар, каждый с полной силой.*

**Tier Scaling:**
| Тир | Stun длит | Макс. врагов | Урон ударов | Follow-up множитель | Cooldown |
|---|---|---|---|---|---|
| T5 | 2.5 sec | 1 (2 на T5+) | 4.0× каждый | ×2.0 | 18 sec |

**Visual:** Чёрный вихрь втягивает врага, потом красный/голубой/зелёный взрывы при ударах.  
**Lore:** "Якорь пустоты — врата в небытие, откуда нет спасения врагу."  
**Synergy (passive):** 5+ of all schools → Pull radius extends, может захватить 2 врагов.

---

## 7. Drop Tables per Biome

| Биом | Level | Common Drops | Rare Drops (T3) | Boss Drops (T4+) |
|---|---|---|---|---|
| **Ice Castle** | 1-2 | Crimson Core, Frostbite Sigil, Permafrost Cube | Glacial Hourglass | (None, go to next biome) |
| **Fire Desert** | 2-3 | Crimson Core, Pyrohelm, Bloodforge, Verdant Spore | Stormcaller | Meteor Strike T4 |
| **Ancient Forest** | 3-4 | Verdant Spore, Mossheart, Lifeweave, Frostbite Sigil | Toxin Vial, Nature's Embrace | Pandemic Bloom T4 |
| **Storm Peak** | 4-5 | Bloodforge, Glacial Hourglass, Lifeweave, Tideforge Crown | Aegis Wreath, Tidal Wave | Frostforge Hammer T4 |
| **Cosmic Vault** | 5-6 | All schools mixed (equal chance) | All T3+ | Prism Crystal T5, Void Anchor T5, Tideroot Sceptre T4 |

**Drop rates:**
- **Common** (60%): T1 artifacts or stacks of lower-tier copies
- **Rare** (30%): T2-T3 unique artifacts
- **Boss drops** (10%): T4+ only, including cross-school hybrids

---

## 8. Tier Evolution Example

**Игровой путь за 3-4 runs:**

1. **Run 1** — Ice Castle: drops 3× Crimson Core T1, 2× Frostbite Sigil T1
2. **Run 2** — Fire Desert: drops 3× Crimson Core T1 (already have 3), 2× Pyrohelm T1, 1× Crimson Core T2
3. **Inventory после Run 2:** 6× Crimson Core T1, 1× Crimson Core T2, 2× Frostbite Sigil T1, 2× Pyrohelm T1
4. **Merge phase:** Player merges 2× Crimson Core T1 → 1× Crimson Core T2 (now has 2× T2 total)
5. **Merge phase 2:** Player merges 2× Crimson Core T2 → 1× Crimson Core T3
6. **Run 3** — Ancient Forest: drops 2× Crimson Core T1, Verdant Spore T1, Lifeweave T2
7. **By end Run 3:** Player has 1× Crimson Core T3, ready to merge into T4 after 2 more copies

**T5 requirements:**
- Requires 32 T1 artifacts of same type (or 16 T2s, or 8 T3s, or 4 T4s, or 2 T5s merge)
- Possible within 5-7 "normal difficulty" runs
- T4-T5 legendary cross-school artifacts require 2-3 different schools of same tier

---

## 9. Active Ability Balance Philosophy

**Reference points:**

1. **T3 baseline** — все активные способности баланса вокруг T3 как стандартной ссылки:
   - Missile Volley T3 = 3 projectiles, 2.5× damage per, 8 sec cooldown
   - Frost Barrier T3 = 40% shield, 5 sec duration, 10 sec cooldown
   - Plague Swarm T3 = 3 drones, standard poison stack, 10 sec cooldown

2. **T1 — learning tier:**
   - 50% эффекта T3
   - Дольше cooldown (обычно +20-30% к T3)
   - Идеально для новичков, которые видят механику впервые
   - Пример: Crimson Core T1 имеет 2 projectiles против 3 в T3

3. **T5 — endgame fantasy:**
   - 200% эффект T3 (или даже 250% для некоторых)
   - Значительно сниженный cooldown (обычно -25-40% от T3)
   - Может добавляться дополнительный эффект (crit chance, secondary status, etc.)
   - Пример: Crimson Core T5 имеет 5 projectiles + 25% crit, cooldown 6 sec vs 8 sec на T3

4. **Cooldown range:**
   - Быстрые способности (single-target, instant): 6-10 sec
   - Средние (area, with delay): 10-15 sec
   - Медленные (powerful, long setup): 15-25 sec
   - Ни одна способность не может быть ниже 6 sec (T5) или выше 25 sec (T1)

5. **Никакая ability не должна trivialize combat:**
   - Нет one-shot kill механик даже на T5 с full setup
   - Самый мощный артефакт (Void Anchor) требует 18 sec cooldown и ограничен 1 целью
   - Каждый артефакт имеет трейдоффы (урон vs защита, контроль vs урон, etc.)

6. **Synergy scaling:**
   - Каждая школа получает +15-20% бонус при 3+ артефактах одной школы
   - Cross-school комбинации (2+ школы) добавляют +20-30% бонусов
   - 5+ of all schools (Prism Crystal, Void Anchor owners) — максимальный синергия

---

## 10. Implementation Priority (Ice Castle Prototype)

**Phase 1 — Vertical Slice (Week 1-2):**
1. **Crimson Core** (T1-T3 complete)
   - Missile Volley активная способность + animations
   - Merge system T1→T2→T3
   - Drop логика в Ice Castle

2. **Frostbite Sigil** (T1-T3 complete)
   - Frost Barrier активная способность
   - Shield механика и визуалы
   - Drop логика

3. **Verdant Spore** (T1-T3 complete)
   - Plague Swarm дроны и poison стеки
   - Drone AI (follow + attack)
   - Synergy система (3+ Red → +15% damage)

**Phase 2 — Depth (Week 3-4):**
4. **Permafrost Cube** (T1-T3)
   - Ice Spike piercing механика
   - Chain freeze логика
   - Multi-target поддержка

5. **Mossheart** (T1-T3)
   - Root Strike контроль механика
   - Damage multiplier для rooted врагов
   - Визуальные эффекты корней

**Phase 3 — Polish (Week 5):**
- T2 версии всех 5 артефактов (merge outputs)
- T3 редкие дропы (Glacial Hourglass, Stormcaller teaser)
- Drop таблицы балансировка
- Анимации активации и cooldown rings

**Phase 4 — Expansion (Post-Vertical):**
- T4-T5 версии
- Остальные 10+ артефактов (Bloodforge, Pyrohelm, Lifeweave, etc.)
- Cross-school гибриды (T4-T5)
- Boss drops логика
- Пост-босс биомы (Storm Peak, Cosmic Vault)

**T2-T3 версии НЕ требуют отдельные spritesheets:**
- Тот же sprite что T1, но с визуальным enhancement (больше glow, particle effects)
- Анимацию скорость увеличивается на +20% за каждый тир
- Cooldown ring визуально отличается (cyan для T2, faint purple для T3)

---

## 11. Implementation Checklist (Engineers)

- [ ] Artifact базовая структура (name, school, tier, ability)
- [ ] Active ability interface (name, cooldown, execute)
- [ ] Tier scaling таблица система
- [ ] Merge логика (2× T1 → 1× T2, etc.)
- [ ] Drop таблица система per биом
- [ ] Inventory UI (отобрази артефакты, покажи школу + тир)
- [ ] Active slot system (3 slots, tap to activate, cooldown ring)
- [ ] Ability activation animation + particle effects
- [ ] Cooldown ring visual + sound effect
- [ ] Synergy detection (count Red/Blue/Green artifacts)
- [ ] Cross-school гибрид система
- [ ] T5 legendary drop logic (boss-only)
- [ ] Testing (15 unique artifacts fully functional)

---

## 12. Open Questions (Design Decisions)

1. **Артефакт unlock progression:**
   - Option A: Все артефакты доступны с Day 1 (случайный дроп зависит от биома)
   - Option B: Unlock через story progression (новые артефакты при открытии новых биомов)
   - **Recommendation:** Option A — случайность увеличивает replay value и excitement

2. **Active ability animations:**
   - Каждый артефакт имеет **unique animation** или используются шаблоны?
   - **Recommendation:** Minimal set (projectile, shield, melee, area) + slight variations per artifact

3. **Cross-school T5 drop mechanism:**
   - Boss-only (100% гарантированно) или random rare с +0.5% шансом?
   - **Recommendation:** Boss-only + guaranteed, но 2-3 разных босса с разными T5s

4. **Ability swap/transfer system:**
   - Может ли игрок переносить способность с одного артефакта на другой?
   - **Recommendation:** Нет в Launch, возможно в Seasons/Updates как advanced feature

5. **Artifact lore connectivity:**
   - Должны ли артефакты иметь связную историю мира или просто случайный флавор?
   - **Recommendation:** Loose lore connections — каждый артефакт в своем мире, но намёки на общую историю

6. **Artifact cosmetic upgrades:**
   - Скины артефактов (golden variant, crystal variant)?
   - **Recommendation:** Post-Launch — сначала полностью механика, потом визуальные вариации

---

## 13. Artifact Rarity & Drop Statistics (Reference)

**Target drop distribution (per run, average):**

| Artifact Tier | Expected Count | Rarity | Merge to Next |
|---|---|---|---|
| T1 | 8-12 total | Common | 2× T1 → 1× T2 |
| T2 | 2-4 (merges) | Uncommon | 2× T2 → 1× T3 |
| T3 | 1-2 (rare drops or merges) | Rare | 2× T3 → 1× T4 |
| T4 | 0-1 (only boss) | Very Rare | 2× T4 → 1× T5 |
| T5 | 0 (post-5 runs) | Legendary | Keep or merge ×2 |

**By school distribution (Ice Castle typical):**
- Red: 40% (Crimson Core primary dumb)
- Blue: 35% (Frostbite Sigil primary)
- Green: 25% (Verdant Spore emerging)

---

## 14. Future Expansion Ideas

**Post-Launch content:**
1. **Yellow School** — Lightning/speed, 5 new artifacts
2. **Purple School** — Void/corruption, 5 new artifacts
3. **T6 tier** (if horizontal progression extends) — 3-5 new legendaries
4. **Seasonal artifacts** — limited-time drops with unique mechanics
5. **Artifact fusion** — combine 3 artifacts into 1 enhanced version (advanced mechanic)
6. **Ability transmog** — transfer one artifact's ability to another (Seasons 2+)

---

## Summary

**Artifacts System Overview:**

- **20 total unique artifacts:** 5 Red + 5 Blue + 5 Green + 5 Cross-school
- **5 schools representation:** 3 base (R/B/G) + infinite cross-school possibilities
- **Tier scaling:** T1 (50%) → T3 (100% baseline) → T5 (200%+)
- **Drop biomes:** Ice Castle, Fire Desert, Ancient Forest, Storm Peak, Cosmic Vault
- **Active abilities:** 20 unique mechanics, all balanced around T3 reference
- **Synergy system:** Passive bonuses per school (3+) and cross-school (2+)
- **Legendary T4-T5:** Boss drops, cross-school hybrids, endgame power fantasy

**Launch readiness:** Fully functional system with all 15+ base artifacts (5 per school), T1-T3 mechanics complete. T4-T5 and cross-school legendaries add Post-vertical-slice depth and endgame aspirations.