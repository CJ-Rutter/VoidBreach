# Changelog

All notable changes to Void Breach will be documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project uses [Semantic Versioning](https://semver.org/) (`MAJOR.MINOR.PATCH`).

---

## [Unreleased]

Future ideas being considered. See [PROJECT_NOTES.md](PROJECT_NOTES.md) for the full roadmap.

### Likely next
- Survival skill branch (revive, regen, damage reduction)
- Power skill branch (boss damage, 5th hero)
- Combat speed control (1× / 2× / 4×)
- Sound effects

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
