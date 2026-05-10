# Void Breach — Project Notes

> **For future Claude Code sessions: read this file first.** It contains all the architectural decisions, conventions, and roadmap context for this project so you can jump in without re-deriving everything.

---

## What this is

**Void Breach** is a sci-fi idle dungeon crawler / auto-battler that runs entirely in a single HTML file. The player commands a squad of space marines descending floor-by-floor through a derelict alien vessel. Combat is automatic; the player manages squad composition, gear, and meta-progression.

**Target platform:** Mobile-first web game, designed to eventually be wrapped with Capacitor or PWA Builder for the Google Play Store.

**Tech stack:**
- Single `index.html` file (no build step, no dependencies)
- Vanilla JS, no frameworks
- CSS-in-`<style>`-tag, no external stylesheets
- `localStorage` for persistence
- `pointerdown`/`touchstart`/`click` event handling for cross-device input

---

## Current version

**v0.4.02** — defined in the `GAME_VERSION` constant near the top of the `<script>` block. Displayed in the SYS panel.

Versioning convention: `MAJOR.MINOR.PATCH`
- Patch (0.1.0**1**): bug fixes, balance tweaks, copy changes shipped on their own
- Minor (0.**2**.00): new feature shipped (e.g. new skill branch, new hero, crafting system) — also covers a feature commit that bundles small fixes alongside; no separate patch needed
- Major (**1**.0.00): reserved for the first "complete" release, ideally Play Store launch

Bump the constant whenever you commit a release. Don't bump for WIP commits.

---

## File structure

```
void-breach/
├── index.html             # The entire game. Single file, ~3000 lines.
├── README.md              # Public-facing overview
├── PROJECT_NOTES.md       # This file
├── CHANGELOG.md           # Version history
└── .gitignore
```

With `index.html` as the entry file, enabling GitHub Pages makes the game playable at `https://USERNAME.github.io/REPO_NAME/`.

---

## Architecture inside the HTML

The script is roughly organized into these sections (search for `// ============` comment blocks to navigate):

1. **GAME DATA** — All static definitions: `HERO_CLASSES`, `ENEMY_TYPES`, `BOSSES`, `CHAPTERS`, `LOOT_TABLES`, `RARITY_NAMES`, `RARITY_MULTS`, `RARITY_PREFIXES`
2. **PRESTIGE / SKILL TREE** — `SKILL_TREE`, `SKILL_BRANCHES`, `PRESTIGE_MIN_FLOOR`, `calculateShardReward`, `getSkillLevel`, `getSkillBonus`
3. **GAME STATE** — Two top-level objects: `state` (persisted to localStorage) and `combat` (runtime-only)
4. **UTILITIES** — `format`, `randInt`, `randChoice`, `log`, `renderLog`, `ensureStats`
5. **HERO STATS** — `getHeroStats`, `getHeroLevelCost`, `getHeroUnlockCost`, `levelUpHero`, `unlockHero`
6. **COMBAT SETUP** — `getActiveHeroes`, `setupCombat`, `generateEnemies`
7. **COMBAT TICK** — `combatTick`, `doAttack` (the main game loop)
8. **LOOT GENERATION** — `generateLoot`, `getSellValue`, `getUpgradeCost`, `MAX_UPGRADES`
9. **LOOT ACTIONS** — `equipItem`, `unequipItem`, `sellItem`, `sellAllBelow`, `upgradeItem`, `upgradeEquippedItem`, `autoEquipBest`, `scoreItemForHero`
10. **STORY** — `updateChapter`
11. **RENDERING** — `renderStatus`, `refreshAffordability`, `renderChapter`, `renderUnits`, `renderUnit`, `showDamage`, `animateAttack`, `renderSquad`, `renderLoot`, `renderStats`, `renderPrestige`, `renderStory`, `renderAll`
12. **PRESTIGE ACTIONS** — `buySkill`, `performPrestige`
13. **INPUT HANDLING** — `attachTap`, tab switching
14. **MODAL** — `showModal` and confirm/cancel handlers
15. **SAVE / LOAD** — `saveGame`, `loadGame`, `exportSave`, `importSave`, button wiring, auto-save interval
16. **INIT** — Try-load-or-start-fresh logic, then the main `setInterval(combatTick, TICK_MS)` loop

---

## Key conventions

These patterns recur throughout the codebase. Follow them when adding features:

### `attachTap(element, handler)`
Universal tap helper that handles mobile touch events AND mouse clicks AND pointer events without double-firing. **Always use this instead of `addEventListener('click', ...)`.** Buttons styled for mobile (with `touch-action: manipulation` in CSS) feel laggy without it.

### `refreshAffordability()`
Called whenever credits or scrap change. Updates the status bar AND re-renders the currently visible tab so buttons enable/disable correctly. Hidden tabs are skipped for performance — they get re-rendered when the user switches to them (see tab-switching code).

### `ensureStats()`
Called before any read/write to `state.stats` or `state.prestige`. Backfills missing keys so old save files don't crash on new fields. **Whenever you add a new field to `state.stats` or `state.prestige`, also add a fallback in `ensureStats()`** so existing players don't lose their saves.

### Save migration via `Object.assign(defaults, data.state)`
Inside `loadGame`, the saved state is merged onto a defaults object. This means new top-level fields automatically get their defaults if they're missing in old saves. For nested objects (like `state.heroes.cyborg`), you may need explicit migration logic.

### Tab re-rendering
Tabs that show "live" data only re-render when visible (see `refreshAffordability` and the tab-switching handler). When adding a new tab, remember to add a case in the tab-switch handler:
```js
else if (t === 'newtab') renderNewTab();
```

### Skill effect lookup
Prestige skills declare an `effect` string and `value` number. `getSkillBonus(effectName)` sums up the contribution from all skills with that effect. To add a new skill, add it to `SKILL_TREE` AND wire its effect into wherever it should apply (combat math, loot generation, costs, etc.). Effect names currently in use:
- `hp_pct`, `atk_pct`, `spd_pct`, `crit_chance` (combat)
- `credits_pct`, `scrap_pct`, `hero_cost_reduce`, `upg_cost_reduce` (economy)
- `drop_chance`, `rarity_upgrade`, `item_start_upg`, `sell_pct` (loot)

---

## Game design decisions worth preserving

These were tuned deliberately — change them only with intent:

- **Prestige unlocks at floor 25.** Originally considered 50/100. Floor 25 was chosen for "early access, more frequent runs" — gives players prestige feedback within their first long session.
- **Heroes drop to half level on prestige (rounded up, min 1).** All heroes stay unlocked. Picked over "full reset" to reduce frustration and over "keep all levels" to make prestige feel meaningful.
- **Shard formula:** `floor(sqrt(maxFloor - 24) * 2)`. Floor 25 = 2 shards, 50 = 10, 100 = 18, 200 = 26. Diminishing returns prevent farm-locking on a single floor.
- **Loot upgrade cap is +10**, with each upgrade giving +20% to that item's stats. Costs scale 1.6× per level on top of rarity-based base cost.
- **Boss mechanics:** shield (Maw Overseer F10), summon (Hive Queen F25), enrage (Warlord F50), lifesteal (Devourer F100), all-of-the-above (Sovereign F200).
- **Defeat penalty:** retreat 1 floor on squad wipe. Soft enough to keep engagement but creates pressure.
- **Loot drop chance:** `0.15 + floor * 0.002 + drop_chance_skill_bonus`, capped implicitly by Math.random.
- **Auto-equip scoring** weights stats by hero base values (e.g. medic gets HP-favored gear, psion gets ATK-favored gear). Speed is weighted heavily for everyone since it's the rarest stat.

---

## State shape (top-level keys)

```js
state = {
  credits, scrap,                    // current spendable currency
  floor, maxFloor, chapter,          // progression
  heroes: {
    marine, engineer, medic, psion: {
      level, equipment: { weapon, armor, module }, unlocked
    }
  },
  inventory: [],                     // array of item objects
  storyUnlocked: [1, 2, ...],        // unlocked chapter numbers
  nextItemId: 1,                     // monotonic ID for items
  prestige: {
    shards, totalShardsEarned, runs,
    skills: { skill_id: level }
  },
  stats: {
    startTime, playtimeMs,
    enemiesKilled, bossesKilled,
    enemyKillsByType: { name: count },
    bossKillsByName: { name: count },
    damageDealt, damageTaken, biggestHit, crits, heroDeaths,
    lifetimeCredits, lifetimeScrap,
    itemsFound, itemsSold, itemsUpgraded,
    rarityCounts: { common, uncommon, rare, epic, legendary },
    floorsCleared, bossesEncountered
  }
}
```

## Item shape

```js
{
  id,                  // monotonic, used for equip/sell lookups
  slot,                // 'weapon' | 'armor' | 'module'
  baseName,            // e.g. 'Pulse Rifle'
  rarity,              // 0-4 (common to legendary)
  atk, hp, speed,      // base stats; multiply by RARITY_MULTS[rarity] * upgMult
  displayName,         // e.g. 'Reinforced Pulse Rifle'
  floor,               // floor it dropped from (used for sell value)
  upgrades             // 0-10
}
```

---

## Roadmap (proposed, in priority order)

These were brainstormed but not yet built:

### Near-term content
- **Survival skill branch** — last stand revive, regen field, damage reduction
- **Power skill branch** — boss damage bonus, all-stat per-prestige bonus, unlock 5th hero
- **Hero abilities/passives** — unlocked at level 5/10/25 milestones (e.g. marine damage reduction, engineer AoE grenades, medic auto-revive, psion guaranteed-crit cooldown)
- **2-3 new heroes** — sniper (one-shots low HP), tank (absorbs hits), summoner (drones)

### Crafting & loot depth
- **Item enchantments** — random affixes on rare+ items (lifesteal, thorns, on-kill heal)
- **Crafting bench** — combine 3 same-rarity items to roll one of next tier up
- **Set bonuses** — equipping multiple pieces of same set gives squad buffs
- **Reroll affixes** with scrap

### Content variety
- **Special floor types** every 5 floors: Elite (one tough enemy + guaranteed loot), Vault (chests, no enemies), Boss Rush, Trap floor
- **More chapters/bosses** past floor 200
- **Daily missions** ("Kill 50 Xenos for 500 CR")

### Polish
- **Sound effects + music** — biggest game-feel upgrade for the effort
- **Combat speed control** (1×/2×/4×) — quality-of-life for grinding
- **Skip-floor button** for already-cleared floors (5+ clears = skippable for full rewards)
- **Particle effects** on crits and kills
- **Achievements** with permanent rewards

### Eventual
- **Auto-update notifier** — small `version.txt` in repo, fetched on load to alert outdated players
- **Capacitor wrap for Play Store** — needs app icon, splash screen, privacy policy, $25 dev fee
- **Cloud save sync** — would require a backend; probably skip until there's demand

---

## Build & test loop

There's no build step. To iterate:

1. Edit `index.html`
2. Open it in a browser (Chrome DevTools mobile emulator is good for testing mobile layout)
3. For real device testing: commit + push, then on phone use the **🔄 FORCE RELOAD** button in the SYS tab to bypass cache after a deploy

For local sanity-checking JS syntax without a browser:
```bash
node -e "const fs = require('fs'); const html = fs.readFileSync('index.html', 'utf8'); const m = html.match(/<script>([\\s\\S]*?)<\\/script>/); new Function(m[1]); console.log('OK');"
```

---

## Things to watch out for

- **Don't break save compatibility.** Always add fallbacks in `ensureStats()` and the `Object.assign` defaults in `loadGame`. Players' progress is sacred.
- **Don't use HTML `<form>` tags or `localStorage`/`sessionStorage` from inside any sandboxed artifact preview** — they're blocked. The standalone HTML file works fine because it's loaded directly in the browser.
- **Mobile input is finicky.** Always use `attachTap`, never raw `onclick`. Always include `touch-action: manipulation` on tappable elements.
- **The artifact preview environment caches localStorage differently than a real browser.** Test save/load on actual devices before assuming a save bug exists.
- **`refreshAffordability` is a hot path.** It runs on every combat tick that awards rewards. Don't put expensive operations inside `renderSquad` / `renderLoot` without measuring.
