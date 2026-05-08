# 🌌 Void Breach

> **A sci-fi idle dungeon crawler.** Command a squad of space marines descending floor-by-floor through a derelict alien vessel. Auto-combat, deep loot, prestige progression, and unique boss mechanics.

**Current version:** v0.1.00 (early development)

---

## 🎮 Play

Drop `void_breach.html` into your browser, or play the live build (if GitHub Pages is enabled): **[link to your GitHub Pages URL once deployed]**

The game is mobile-first — works great on a phone in portrait mode. Open the link on your phone and add it to your home screen for an app-like experience.

---

## ✨ Features

- **🪖 4 unlockable heroes**, each with distinct combat roles
  - Kess Vance, Assault Marine — balanced fighter
  - Dr. Nyx Oram, Engineer — glass-cannon DPS
  - Sera Bellis, Field Medic — heals allies on each shot
  - Voss-7, Psion — high crit chance for double damage
- **🎁 Five-tier loot system** — Common → Uncommon → Rare → Epic → Legendary, with weapons, armor, and tech modules
- **⚙ Item upgrades** — spend scrap to upgrade gear up to +10, +20% stats per level
- **👹 Boss fights** every 25-50 floors with unique mechanics
  - Floor 10: Shield regeneration
  - Floor 25: Add summoning
  - Floor 50: Enrage at low HP
  - Floor 100: Lifesteal
  - Floor 200: All mechanics combined
- **📖 5-chapter story** unfolds as you descend
- **💠 Void Protocol prestige system** — earn permanent skill points across 3 branches:
  - ⚔ Combat (HP, ATK, speed, crits)
  - 💰 Economy (drops, costs, sell value)
  - 🎁 Loot (drop chance, rarity upgrades, starting upgrades)
- **📊 Detailed stats tracking** — kills by enemy type, biggest hit, lifetime credits, playtime, and more
- **💾 Robust save system** — auto-save, manual save/load, export/import codes for cross-device sync, offline progression

---

## 🚀 Roadmap

Planned for future releases:

- **🛡 Survival skill branch** — auto-revive, regeneration, damage reduction
- **⚡ Power skill branch** — boss damage bonus, 5th hero unlock
- **🎯 Hero abilities** unlocked at level milestones
- **🛠 Crafting & enchantments** — combine items, reroll stats, set bonuses
- **🗺 Special floor types** — elite floors, vaults, boss rushes
- **🔊 Sound effects and music**
- **⏩ Combat speed control** (1× / 2× / 4×)
- **🏆 Achievements** with permanent rewards
- **📱 Native Android wrapper** for Google Play Store

See [PROJECT_NOTES.md](PROJECT_NOTES.md) for the full design notes.

---

## 🛠 Tech

- **Pure HTML5/CSS/JavaScript** — single file, no build step, no dependencies
- **Vanilla JS** — no frameworks, no bundlers
- **`localStorage`** for persistence
- Designed for mobile browsers first, scales to desktop

---

## 🧑‍💻 Development

This is a solo passion project. Contributions, bug reports, and ideas are welcome via [Issues](../../issues).

To run locally:
```bash
git clone https://github.com/USERNAME/void-breach.git
cd void-breach
# Just open void_breach.html in your browser
```

To deploy via GitHub Pages:
1. Push to `main`
2. Repo Settings → Pages → Source: `main` branch, `/` (root)
3. Game will be live at `https://USERNAME.github.io/void-breach/`
4. (Optional) Rename `void_breach.html` to `index.html` so the URL is cleaner

For active development, see [PROJECT_NOTES.md](PROJECT_NOTES.md) for architecture, conventions, and design decisions.

---

## 📜 License

[MIT](LICENSE) — feel free to fork, learn from, and remix.

---

*Built with ☕ and the help of Claude.*
