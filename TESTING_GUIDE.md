# Testing & Debug Guide — Mûmakan Ring-Lore

Quick reference for validating every system in **debug mode** (launch with `-debug_mode`).
Open the console with `~` (or `²` / `\`` depending on keyboard). Commands act on the character you're playing unless a character ID is given.

> **About "the decision always failed" / "always the 40% ring":** that is **CK3's RNG, not a bug.**
> `random_list` rolls once; if you **reload the same save and re-take immediately, you get the same result every time.**
> To re-roll, let a few in-game days pass first — or, far easier, **force each outcome directly with the console commands below.**

---

## Useful console commands

| Goal | Console command |
|---|---|
| Top up resources | `add_piety 200000` · `add_gold 50000` · `add_prestige 200000` |
| **Force Palier 2 → imperfect Elven ring (Result A)** | `event mumakan_ring.0002` |
| **Force Palier 2 → awakened Akhâsh-Mûmak (Result B)** | `event mumakan_ring.0003` |
| Fire the Mordor-threat event (imperfect path) | `event mumakan_ring.0010` |
| Fire the monthly tribute tick | `event mumakan_ring.0020` |
| Add a custom trait (loads-check) | `add_trait loyalty_to_the_ring` / `add_trait will_of_the_ring` / `add_trait mumakan_ringbound_immortal` |
| Instantly finish a building | `add_realm_law` … (or use the holding "complete construction" debug button) |

---

## Validation checklist (Phase 1 + 2)

### Palier 1 — L'Anneau Mineur  ✅ already confirmed
1. Independent Mûmakan (or Black Númenórean) **feudal/clan** ruler builds the **Forges Maîtresses de Mûmakan** (duchy capital).
2. Decisions → **"Plonger dans le Savoir des Anneaux"** (under *Royaumes en Exil – Décisions Majeures*) → take it.
3. ✔ **L'Anneau Mineur** artifact appears + equips (gives +2 Martial / +5 Vassal Opinion / +5% Knight Effectiveness).
4. ✔ Dynasty → Legacies shows **"La Volonté des Anneaux"** unlocked.

### Palier 2 — L'Anneau Maître (RNG)  ✅ Result A confirmed
- Take **"Forger l'Anneau Maître"** (Piety or Prestige). **Or** force each side to test both:
  - `event mumakan_ring.0002` → **Result A** (imperfect Elven ring; +5 Intrigue/+5 Learning; triggers recurring Mordor pressure).
  - `event mumakan_ring.0003` → **Result B** (awakened) — verify: **Dread = 100** (locked), the **immortal** trait, +50% fertility & big stats on the master ring (vilya visual), and the **primary heir gains "Volonté de l'Akhâsh-Mûmak."**

### Palier 3 — Les Anneaux de Loyauté  ⏳ to test
1. As a ruler who has forged the Master Ring (Result A or B), **right-click a Duke-tier (or higher) internal vassal** → **"Offrir un Anneau de Loyauté."**
2. ✔ Vassal converts to your faith, gains **+100 opinion (locked)**, the **Serviteur de l'Anneau** trait, immortality + fertility, and a Ring of Loyalty artifact.
3. ✔ Hard cap: you can do this **16 times** maximum.

### Dynasty Legacy — La Volonté des Anneaux  ⏳ to test
- Buy the 5 perks in order (Renown). Tier 3 cuts loyalty-ring Piety cost −20%; Tier 5 turns on the 5%/month tribute.

### Time-based systems (run ~years)  ⏳ to test
- **Mordor pressure** (imperfect path): the threat event recurs (`mumakan_ring.0010`).
- **Tribute** (Tier 5 + ring-bound vassals): `mumakan_ring.0020` transfers 5%/month to the bearer.
- **Succession**: when the Master-Ring bearer dies, the heir inherits bearer status + the 16-ring counter + the will trait.
- **Cadet scission**: a ring-bound servant's house spins off a cadet branch past 15 living members.
- ✔ Run ~50 in-game years and watch `error.log` stays clean.

---

## Custom artwork — what to send

CK3 uses **`.dds`** (not PNG/JPEG). Send the source PNG/JPEGs named clearly and I'll convert + wire them in:

| Asset | Send as | Final format / size | Hooked into |
|---|---|---|---|
| Forges building icon | PNG | `.dds`, ~64×80 px | `type_icon` |
| Decision illustration(s) | PNG/JPEG | `.dds`, ~536×340 px | decision `picture` |
| Event background(s) | PNG/JPEG | `.dds`, ~536×340 px | event background |
| Ring artifact icon(s) | PNG | `.dds`, 240×240 px | custom artifact `visuals` |

(3-D ring models are very high effort and not recommended — 2-D icons/illustrations are quick.)
