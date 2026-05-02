# 10 — Tech Requirements

## 1. Engine & Stack

**Recommended Engine:** Unity 2022.3 LTS (mobile-friendly, good performance for hybrid 2D+3D)
**Alternative:** Unreal Engine 5 (если требуется более premium 3D dungeon visuals)

**Language:** C# (Unity) или C++/Blueprints (Unreal)
**Backend:** PlayFab или Firebase (для leaderboards, save sync, daily seed)
**Analytics:** GameAnalytics или Adjust (для UA tracking)
**Crash reporting:** Sentry или Firebase Crashlytics

## 2. Target devices

### Min spec (P95 device)
- **iOS:** iPhone 8, iOS 14+
- **Android:** Snapdragon 660 / equivalent, Android 10+, 3 GB RAM
- **Performance:** stable 30 fps acceptable

### Target spec (P50 device)
- **iOS:** iPhone 11, iOS 15+
- **Android:** Snapdragon 765 / Mediatek Dimensity 700+, Android 12+, 4-6 GB RAM
- **Performance:** locked 60 fps target

### High spec (P10 device)
- **iOS:** iPhone 14+
- **Android:** Snapdragon 8+ Gen 1, 8+ GB RAM
- **Performance:** 60 fps + maximum effects + 120 fps option

## 3. Performance budgets

### Frame rate
- **Combat phase:** locked 60 fps (P50+), graceful degrade to 30 fps если sustained <60 measured 3 sec
- **Lobby phase:** 60 fps standard (low GPU load)
- **Hub:** 30-60 fps (less critical)
- **Slow-mo fallback:** combat 50% speed if drops <20 fps (preserves playability)

### Input latency
- **Tap → action:** <100 ms (P50 device)
- **Swipe → dodge:** <100 ms swipe-to-displacement (CRITICAL for combat feel)
- **Validation gate:** mandatory latency test before vertical slice greenlight

### Memory
- **Total app memory:** <500 MB на P50, <800 MB на high-end
- **Texture memory:** <200 MB (compressed ASTC for iOS, ETC2 for Android)
- **Audio memory:** <50 MB (compressed)
- **Code memory:** <100 MB

### Loading times
- **Cold start to Hub:** <8 sec (P50), <12 sec (P95)
- **Lobby phase load:** <0.5 sec (transition from combat)
- **Combat phase load:** <0.5 sec (lobby → combat)
- **Biome change:** <2 sec (carry-over → event → next biome)
- **Full run resume from cold start:** <5 sec

### Battery
- **Combat sustained:** <8% battery per 30 min (active gameplay)
- **Idle/hub:** <2% battery per 30 min (paused / menu)
- **Background:** zero drain

## 4. 3D rendering specs (combat phase)

### Polygon budgets
- **Hero (FPV view, hands/weapon):** <8K triangles total
- **Enemy basic:** <2K triangles each
- **Enemy elite/boss:** <8K triangles
- **Environment (1 corridor segment):** <15K triangles
- **VFX particles:** <50 simultaneous active particles

### Texture budgets
- **Hero textures:** 1024×1024 max (diffuse + normal + roughness)
- **Enemy textures:** 512×512 max
- **Environment textures:** 2048×2048 max (tiled)
- **UI textures:** 512×512 max per element
- **Total VRAM target:** <150 MB

### Lighting
- **Combat phase:** 1 directional light + 2 point lights max
- **Lobby phase:** 1 ambient + 1 directional (low cost)
- **Real-time shadows:** только hero + 1-2 nearest enemies (cascade shadows)
- **Baked lighting:** environments use static lightmaps

### Particle effects
- **Synergy procs:** 5-15 particles per effect, 2 sec lifetime
- **Burst activations:** 30-50 particles, 1 sec
- **Combat impacts:** 10-20 particles per hit
- **Boss attacks:** 50-100 particles, 3 sec
- **LOD system:** при 30+ effects → reduce count by 50%
- **Quality scale:** high/medium/low tied to device

### Audio
- **Combat SFX:** 8-16 simultaneous sounds (mix priority)
- **Music:** 1 stream, biome-specific track
- **Voice/grunts:** 4 simultaneous max
- **Compressed Vorbis** для music, **PCM 22kHz** для SFX
- **Spatial audio:** 3D positioning для enemies (стерео pan based on screen X)

## 5. 2D rendering specs (lobby + UI)

### Lobby phase backpack
- **Grid cells:** sprite-based, 80×80 px each
- **Artifact icons:** 64×64 px sprite atlas (3 schools × 4 tiers = 12 unique sprites + variants)
- **Animations:** Spine 2D или native sprite animation, 30 fps acceptable

### UI elements
- **HUD overlay (combat):** Unity uGUI или TextMesh Pro
- **Modal dialogs:** Canvas overlay
- **Cooldown rings:** custom shader (radial fill)
- **Damage numbers:** TextMesh Pro pool (max 30 active)

### Resolution scaling
- **Reference resolution:** 1080×1920 (9:16 portrait)
- **Min support:** 750×1334 (iPhone SE)
- **Max support:** 1290×2796 (iPhone 14 Pro Max)
- **Safe area handling:** notch/dynamic island padding

## 6. Networking & backend

### Save data
- **Local save:** primary, all progression stored on-device
- **Cloud sync:** PlayFab/Firebase Cloud Functions
- **Sync frequency:** on app foreground/background, post-prestige, post-Battle Pass purchase
- **Conflict resolution:** server timestamp wins

### Save data size
- **Hero profile:** <5 KB (level, stats, hero board state)
- **Codex (24 synergies):** <2 KB
- **Run history (last 50 runs):** <50 KB
- **Total save:** <100 KB compressed

### Online features (MVP)
- **Weekly Challenge seed:** synced from server every Monday 00:00 UTC
- **Leaderboard:** PlayFab Statistic API
- **Daily quests:** server-validated, anti-cheat
- **IAP receipt validation:** server-side (App Store / Google Play)

### Offline behavior
- All gameplay works offline
- Online features (leaderboard, weekly challenge) показывают cached data
- IAP requires online (standard)
- Save sync at next online session

### Anti-cheat
- Critical actions validated server-side (IAP, leaderboard submissions)
- Rate limiting на API endpoints
- Encrypted save files (light obfuscation, не CRITICAL для casual game)

## 7. Asset pipeline

### Source formats
- **3D models:** FBX (Blender / Maya export)
- **Textures:** PNG (sRGB) → compressed на build (ASTC/ETC2)
- **Audio:** WAV 44.1kHz → Vorbis для music, ADPCM для SFX
- **Animations:** Unity Mecanim или Spine 2D

### Build pipeline
- **Asset bundles:** dynamic loading per biome (reduces initial download)
- **Initial download:** <150 MB (App Store / Google Play limits compliance)
- **Total game size после downloads:** <500 MB
- **OBB не требуется** (Android Play Asset Delivery instead)

### CI/CD
- **Build server:** Unity Cloud Build или GitHub Actions
- **Test devices:** TestFlight (iOS) + Firebase App Distribution (Android)
- **Daily builds** для QA
- **Weekly TestFlight** для closed beta

## 8. Quality scaling system

3 tiers detected automatically by device:

| Tier | Device | Quality settings |
|---|---|---|
| **High** | iPhone 14+, Snap 8 Gen+, 60 fps stable | Max textures, all effects, real-time shadows |
| **Medium** | iPhone 11-13, Snap 7XX, 60 fps with minor drops | Reduced textures (-25%), simplified shadows, LOD particles |
| **Low** | iPhone 8/X, Snap 660, 30 fps target | Minimum textures, no real-time shadows, particle count -50% |

User can manually override через Settings → Graphics Quality.

## 9. Latency optimization

### Input pipeline
- **Touch input:** Unity Input System (low-latency mode)
- **Frame buffer:** triple-buffered, no V-sync delay
- **Render target:** native resolution (no scaling) для P50+ devices

### Network
- **Async APIs:** все network calls non-blocking
- **Timeout:** 5 sec per call, retry 3 times with exponential backoff
- **Caching:** aggressive client-side cache для leaderboards, codex, profile

### Combat-specific
- **Predictive input:** swipe registered immediately, animation interpolates
- **Forgiveness window:** 33ms input buffer (allows late inputs to register)
- **Jitter reduction:** dodge animation timing locked to 60 fps frame boundaries

## 10. Compatibility matrix

### iOS
- **Min OS:** iOS 14
- **Architectures:** arm64
- **Capabilities:** GameKit, In-App Purchase, Push Notifications

### Android
- **Min API:** 26 (Android 8.0)
- **Target API:** 34 (Android 14)
- **Architectures:** arm64-v8a (primary), armeabi-v7a (legacy support)
- **Permissions:** INTERNET, ACCESS_NETWORK_STATE, BILLING

### Privacy & compliance
- **GDPR:** consent required EU
- **CCPA:** opt-out California
- **COPPA:** age gate если targeting <13
- **App Tracking Transparency (ATT):** iOS 14.5+ tracking dialog

## 11. Tools & integrations

| Tool | Purpose |
|---|---|
| Unity 2022.3 LTS | Engine |
| Visual Studio / Rider | IDE |
| Git + Git LFS | Version control |
| GitHub Actions / Unity Cloud Build | CI |
| Sentry | Crash reporting |
| GameAnalytics | Behavioral analytics |
| Adjust | UA attribution |
| PlayFab | Backend (saves, leaderboards) |
| TestFlight + Firebase App Distribution | Beta testing |
| Helpshift | Customer support |
| Bugsnag | Error tracking |

## 12. Validation gates (engineering checkpoints)

| Phase | Gate | Pass criteria |
|---|---|---|
| **Prototype (M1-2)** | Latency feel test | <100 ms input, 60 fps locked на P50 |
| **Vertical Slice (M3-4)** | Performance audit | Memory <400 MB, no leaks, frame time <16ms |
| **Closed Beta (M5-6)** | Stability | <0.5% crash rate, 95th %ile session length >5 min |
| **Soft Launch (M7-8)** | Backend load | 10K concurrent users tested, <500ms API response |
| **Global Launch (M9)** | Final QA | All edge cases tested, A11y compliant, GDPR/CCPA готов |

## 13. Open questions

1. Engine choice: Unity 2022.3 LTS vs Unity 6 (новее, более features)?
2. 3D first-person view — ray-casted geometry или full 3D models?
3. Backend: PlayFab (more features) vs Firebase (cheaper)?
4. Push notifications strategy — daily reminders или smart timing?
5. Cross-platform progression — iOS↔Android sync через email или Apple/Google ID?
6. Cloud save conflict resolution UI — automatic merge или player choice?
7. ATT prompt timing — first launch или after tutorial?
