# Setup & Player Guide — Mûmakan Ring-Lore & Shadow Sovereignty

A standalone sub-mod for **Realms in Exile v4.3** (CK3 base **1.19.x**). It is **not** a
total conversion: it adds only new files and never overwrites RoE or base-game content.
**Realms in Exile must be enabled**, and this sub-mod must load **below** (after) it.

---

## 1. Installation

### A. Local / manual
1. Copy the folder `mod/mumakan_ring_lore/` into your CK3 user mod directory:
   `Documents/Paradox Interactive/Crusader Kings III/mod/`
2. Copy `mod/mumakan_ring_lore.mod` into that same `mod/` folder (next to the folder).
3. Launch CK3 → **Mod Manager** → enable **Lord of the Rings: Realms in Exile** and
   **Mumakan Ring-Lore - Shadow Sovereignty**.
4. **Load order:** Realms in Exile first, this sub-mod last.

### B. Steam Workshop upload
1. In the CK3 launcher, *Tools → Upload Mod*, pick this mod, and upload.
2. Drop a square `thumbnail.png` (1:1, ≤ 1 MB) into `mod/mumakan_ring_lore/` first — a
   placeholder is included; replace it with real art before publishing.
3. After upload, set the real Workshop `remote_file_id` if you want it embedded in the descriptor.

### Multiplayer
All players must run **identical** mod lists and load order (use a Steam Collection). All ring
crafting is player-only and AI logic is deliberately minimal, so the mod is desync-safe; just keep
debug mode **off** during MP sessions.

---

## 2. Progression walk-through

1. **Be eligible.** Play an **independent** adult ruler of a **Mûmakan** (`heritage_mumakani`)
   or **Black Númenórean** (`heritage_southern_adunaic`) culture, under **Feudal or Clan**
   government. (Tribal rulers must reform first.)
2. **Build the Forges Maîtresses de Mûmakan** in your duchy **capital** holding. This grants the
   Mûmakil combat bonuses and unlocks the Ring-Lore decisions.
3. **Palier 1 — Plonger dans le Savoir des Anneaux** (Decisions panel): pay 20 000 Piety + 5 000
   Gold to forge **L'Anneau Mineur**. The physical ring appears in your inventory and equips
   itself; the dynasty legacy **La Volonté des Anneaux** unlocks.
4. **Palier 2 — Forger l'Anneau Maître**: pay 100 000 Piety **or** 100 000 Prestige (two decision
   variants). Outcome is random — 40% imperfect Elven ring, 60% the awakened Akhâsh-Mûmak.
5. **Palier 3 — Offrir un Anneau de Loyauté**: right-click a **Duke-tier or higher internal
   vassal** and choose the interaction. Costs 40 000 Piety each. **You may forge at most 16 ever.**

### The strict tier hierarchy
- You cannot start Palier 2 until `has_forged_minor_ring`.
- You cannot start Palier 3 until `has_forged_master_ring` (either outcome).
- The 16-ring cap is tracked on the bearer and **carries across succession** — your heir inherits
  the chain *and* the remaining count, it does not reset.

### Dynasty legacy (La Volonté des Anneaux)
Bought with Dynasty Renown like any legacy, in strict order T1→T5. T3 cuts loyalty-ring piety cost
by 20%; T5 turns on the 5%/month gold tribute from your ring-bound vassals.

---

## 3. Limitations & design notes (read before testing)

These are deliberate, documented choices to keep the mod **error-free and RoE-compatible**:

1. **No base/RoE files overwritten, no `defines` edits.** RoE heavily edits defines; overriding
   them risks conflicts and checksum drift, so all tuning lives in `common/script_values/`.
2. **Dread "lock at 100"** uses a `dread_baseline_add = 100` character modifier (raises the resting
   dread to max) plus an immediate `add_dread = 100` and a yearly top-up. CK3 has no literal
   `set_dread`/lock primitive, so this is the robust equivalent — functionally pinned at 100.
3. **Immortality** is a hidden trait (`mumakan_ringbound_immortal`, `immortal = yes`). It stops
   aging/old-age death; it cannot stop *every* scripted death (murder, war), by engine design.
4. **Faction immunity** is enforced by a yearly cleanup that ejects `loyalty_to_the_ring` holders
   from any faction (`leave_faction`). CK3 has no per-character "cannot join faction" flag, and we
   refuse to override RoE's faction files, so ejection is the override-free solution. Granularity
   is yearly.
5. **Monthly tribute** (legacy T5) is a self-scheduling monthly event on the bearer (CK3 has no
   monthly on_action pulse). It transfers **5% of each ring-bound vassal's current treasury**; if
   you prefer 5% of *income*, swap `gold` for a monthly-income value in
   `common/script_values/mumakan_values.txt` after confirming the read-value in your build.
6. **Artifact creation-time −20%** from the building was **omitted** — no artifact-creation-time
   character-modifier key could be confirmed in the live files. Add it to
   `common/buildings/mumakan_forges_buildings.txt` once you verify the key, if desired.
7. **Imperfect Elven ring downside.** The "−50% hostile scheme resistance" is realized as recurring
   live Mordor pressure (event `mumakan_ring.0010`) rather than a raw modifier, because the exact
   scheme-resistance modifier key was unverifiable. To also apply the raw stat, add the confirmed
   key to `mumakan_elven_ring_modifier` in `common/modifiers/mumakan_modifiers.txt`.
8. **Reused art.** Rings reuse RoE ring visuals (`narya`, `nenya`, `vilya`, `ring_of_men`); the
   building reuses `icon_building_hall_of_heroes.dds`; traits reuse vanilla icons. **Zero new
   textures ship**, so there are no missing-asset errors.
9. **Eligible cultures** are gated on RoE's `heritage_mumakani` / `heritage_southern_adunaic`
   pillars (see `common/scripted_triggers/mumakan_triggers.txt`). Add more `OR` pillars there if
   you want additional southern cultures to qualify.

---

## 4. Testing checklist (success criteria)

- [ ] Mod loads with **no new lines in `error.log`** (RoE + sub-mod enabled, load order correct).
- [ ] As an eligible Mûmakan/Black Númenórean Feudal/Clan ruler, the **Forges** building is
      constructible in the duchy capital.
- [ ] After building it, **Plonger dans le Savoir des Anneaux** appears and is clickable; taking it
      spawns and equips **L'Anneau Mineur** and unlocks the legacy track.
- [ ] **Forger l'Anneau Maître** yields one of the two outcomes; the awakened ruler shows Dread 100,
      immortality, and the heir gains **Volonté de l'Akhâsh-Mûmak**.
- [ ] **Offrir un Anneau de Loyauté** converts a Duke+ vassal, locks +100 opinion, and stops at 16.
- [ ] Run **~50 in-game years**: succession transfers the bearer chain; tribute flows with legacy
      T5; no error.log spam; stable in multiplayer checksum.

`error.log` lives at `Documents/Paradox Interactive/Crusader Kings III/logs/error.log`.
Use launch option `-debug_mode` while testing (turn it **off** for multiplayer).
