# Changelog — Mûmakan Ring-Lore & Shadow Sovereignty

All notable changes to this sub-mod. Target: **Realms in Exile v4.3** (CK3 base 1.19.x).

## [0.1.0] — Initial functional build

First playable build. Implements the full design document as a standalone sub-mod
(no Realms in Exile or base-game files are overwritten).

### Infrastructure
- **Forges Maîtresses de Mûmakan** — new Duchy-tier building (`type = duchy_capital`),
  restricted to Mûmakan (`heritage_mumakani`) or Black Númenórean (`heritage_southern_adunaic`)
  capitals under **Feudal or Clan** government.
  - +30% Mûmakil men-at-arms damage and +30% toughness (via `elephant_cavalry_*`, exactly as
    RoE buffs the `war_mumak` unit).
  - +10% monthly Piety and +10% monthly Prestige.
  - Unlocks the Ring-Lore decisions.

### Ring-Crafting Progression (player-only)
- **Palier 1 — Plonger dans le Savoir des Anneaux** (decision): 20 000 Piety + 5 000 Gold.
  Forges **L'Anneau Mineur** as a real equipped artifact (+2 Martial, +5 Vassal Opinion,
  +5% Knight Effectiveness), sets `has_forged_minor_ring`, and unlocks the dynasty legacy.
- **Palier 2 — Forger l'Anneau Maître** (decision, Piety *or* Prestige variant): 100 000.
  - 40% **Le Chef-d'Œuvre Imparfait** — Elven-tier ring (+5 Learning, +5 Intrigue) that draws
    recurring Mordor subversion pressure.
  - 60% **L'Akhâsh-Mûmak Éveillé** — the Unique: +10 Martial, +10 Prowess, +5 Intrigue,
    immense health, +50% fertility, Dread locked at 100, immortality, and `will_of_the_ring`
    granted to the primary heir.
- **Palier 3 — Offrir un Anneau de Loyauté** (character interaction on a Duke+ internal vassal):
  40 000 Piety (−20% with legacy tier 3). **Hard cap of 16.** Converts the vassal to your faith,
  makes them a devoted immortal servant (+30% fertility), locks **+100 opinion**, and bars them
  from all factions against you.

### Traits
- **Serviteur de l'Anneau** (`loyalty_to_the_ring`) — +5 Prowess, +2 Learning; faction immunity;
  passes to the primary heir at full weight.
- **Volonté de l'Akhâsh-Mûmak** (`will_of_the_ring`) — +3 Martial, +3 Intrigue, +15% Dread gain,
  −10 general opinion, +10% monthly stress; passes to the primary heir.
- **Lié à l'Anneau** (`mumakan_ringbound_immortal`) — hidden longevity trait (stops aging).

### Dynasty Legacy — La Volonté des Anneaux
- 5-tier track unlocked globally once the Minor Ring is forged: Maîtrise du Savoir des Anneaux →
  Majesté Absolue → Protocoles d'Assujettissement (−20% loyalty-ring piety) → Garde des Serviteurs
  → Tribut des Sans-Âme (5% monthly gold tribute from ring-bound vassals).

### Succession & automation
- The Shadow Sovereignty chain (bearer status, the 16-ring counter, `will_of_the_ring`) transfers
  cleanly to the primary heir on death.
- Ring-bound servants whose house exceeds 15 living members trigger an automatic cadet-branch
  scission to prevent family bloat.

### Localization
- Full French in-game nomenclature in both `english` and `french` localization (UTF-8 + BOM).

### Compatibility / stability
- Standalone: adds only new files; **no base-game or RoE file is overwritten**; **no `defines` edits**.
- All crafting is **player-only** (`is_ai = no`) to protect balance and multiplayer stability.
- Built-in on_actions (`on_death`, `yearly_playable_pulse`, `on_game_start`) are *hooked*, not
  redefined, so RoE's own logic is preserved.

### Known engine-limitation handling
See `SETUP.md` → *Limitations & design notes* for the (documented) handling of dread-locking,
faction immunity, monthly tribute, and the omitted artifact-creation-time modifier.
