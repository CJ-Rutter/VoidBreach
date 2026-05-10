# Changelog

All notable changes to Void Breach will be documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project uses [Semantic Versioning](https://semver.org/) (`MAJOR.MINOR.PATCH`).

---

## [Unreleased]

Future ideas being considered. See [PROJECT_NOTES.md](PROJECT_NOTES.md) for the full roadmap.

### Likely next
- Squad-selection UI (pick 4 of N unlocked heroes) — prerequisite for expanding the roster past 4
- 3 new heroes (Sniper / Tank / Summoner)
- Power skill branch (boss damage, 5th hero unlock)
- Sound effects

---

## [0.4.01] — Scaling Rebalance

### Changed
- **Enemy HP/ATK floor scaling softened** — regular enemies scale at `1.18^floor` HP / `1.15^floor` ATK (was `1.22` / `1.18`). Bosses scale at `1.16^floor` HP / `1.15^floor` ATK (was `1.25` / `1.20`). This was the root cause of mid-game becoming unwinnable: enemy stats were outrunning hero leveling exponentially each floor. Surfaced clearly when 4× speed let players advance faster than they could level.
- **Boss `hpMult` / `atkMult` rebalanced** — F10 5/1.4, F25 8/1.7, F50 12/2.2, F100 18/3.0, F200 25/4.0 (was 8/1.5, 12/1.8, 20/2.5, 40/3.5, 100/5.0). The F200 Sovereign at the old 100× hpMult was producing HP values past `Number.MAX_SAFE_INTEGER` (~9×10¹⁵), where integer math silently loses precision.

### Fixed
- F200 Sovereign HP no longer overflows JS safe-integer range; final boss is now actually representable.

---

## [0.4.00] — Combat Speed Control

### Added
- **Combat speed control** — toggle between **1× / 2× / 4×** from the SYS tab. Persists across saves. Auto-save and playtime tracking remain on real-time so faster speeds don't distort either.

### Changed
- `PROJECT_NOTES.md` — current-version pointer fixed (was stale at 0.1.00) and Minor-bump rule clarified to cover feature commits that bundle small fixes.

---

## [0.3.00] — Hero Abilities

### Added
- **Hero abilities** — each of the 4 heroes gains 3 level-gated abilities (unlocked at Lv5/10/25), surfaced on the squad cards as you level up. No save migration needed; abilities are pure level-derived.
  - 🪖 **Marine** — *Bulwark* (-10% damage taken), *Counter-Strike* (25% retaliate on hit, ½ damage), *Squad Discipline* (+5% squad attack aura while alive)
  - 🔧 **Engineer** — *Frag Grenade* (every 5th attack hits all enemies for 75%), *Overcharge* (+1% atk per 1% missing HP), *Siege Mode* (+50% damage to bosses)
  - 💊 **Medic** — *Triage* (heal doubles on allies below 30% HP), *Stim Surge* (squad +10% HP on floor clear), *Resurrection Protocol* (once-per-battle 1 HP save; stacks with Last Stand)
  - 🔮 **Psion** — *Mind Spike* (crits do 3× instead of 2×), *Synaptic Echo* (15% chance to attack twice), *Void Resonance* (each crit cuts squad cooldowns by 0.15s)

---

## [0.2.00] — Survival Branch

### Added
- **🛡 Survival skill tree branch** with 3 new prestige skills:
  - **Reactive Plating** (5 levels) — -3% squad damage taken per level
  - **Regeneration Field** (3 levels) — +1% squad max HP/sec passive regen per level
  - **Last Stand** (3 levels) — first fallen hero per battle revives at +25% HP per level

---

## [0.1.00] — Initial Build

### Core gameplay
- Auto-battler combat with squad of up to 4 heroes vs randomized enemies
- 200+ floor descent with 5 narrative chapters
- 4 unlockable heroes with distinct roles (Marine, Engineer, Medic, Psion)
- 5 boss fights at floors 10, 25, 50, 100, 200 with unique mechanics:
  - Shield regeneration (Maw Overseer)
  - Adds summoning (Hive Queen)
  - Enrage at low HP (Warlord Krex-Tor)
  - Lifesteal (Devourer)
  - All mechanics combined (Null-Sovereign Xal)
- Squad wipe = retreat 1 floor (soft punishment)

### Progression
- Hero level-up system with credits
- 5 rarity tiers of loot (Common → Legendary)
- Item upgrade system (+1 to +10) with scrap, +20% stats per level
- Auto-equip "best gear" button with per-hero stat weighting
- Sell items individually or in bulk by rarity tier (color-matched buttons)
- Quick-sell options: Commons, ≤Uncommon, ≤Rare, All

### Prestige system (Void Protocol)
- Unlocks at floor 25
- Earn Void Shards 💠 based on highest floor reached (sqrt curve)
- Heroes drop to half their level on prestige; remain unlocked
- Skill tree with 12 nodes across 3 branches:
  - ⚔ Combat: Squad Discipline, Calibrated Aim, Trigger Discipline, Critical Network
  - 💰 Economy: Salvage Protocols, Scrap Reclamation, Bargain Hunter, Forge Mastery
  - 🎁 Loot: Lucky Find, Quality Control, Treasure Hunter, Auction Network

### UI / UX
- Mobile-first design with cross-platform tap handling (`pointerdown`, `touchstart`, `click`)
- 6-tab navigation: Squad, Loot, Void, Stats, Story, Sys
- Sci-fi terminal aesthetic with grid overlay and neon glow effects
- Animated combat (attack lunges, hit flashes, floating damage numbers)
- Boss bar with shield indicator
- Live combat log with chapter/loot/crit/boss color coding

### Stats tracking
- Playtime, floors cleared, highest floor reached
- Enemies killed (with breakdown by type, top 5)
- Bosses defeated with per-boss kill counts
- Damage dealt/taken, biggest single hit, crits landed, hero deaths
- Lifetime credits and scrap, items found/sold/upgraded
- Loot rarity counts (commons → legendaries collected lifetime)

### Save system
- Auto-save every 15 seconds and on tab close/visibility change
- Manual save/load buttons
- Export/import save codes (base64-encoded JSON) for cross-device sync
- Offline progression — earn passive credits while away (capped at 8 hours)
- Forward-compatible save migration via `ensureStats()` defaults

### System
- Force Reload button (cache-bust via timestamp query string) for testing updates
- Version display in SYS panel
- Reset all data option with confirmation modal
