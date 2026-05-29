# Mûmakan Ring-Lore & Shadow Sovereignty — Build Spec (internal)

This is the canonical identifier registry and engineering-decision log. Every script and
localization file in the mod MUST cross-reference these exact identifiers. It lives in the repo
root (NOT inside the shipped mod folder) so it never affects the game checksum.

Target: **Realms in Exile v4.3** sub-mod. Fully standalone — adds only new custom files, never
overwrites or patches RoE/base-game files. No `common/defines/` overrides (deliberate, see §9).

Namespace prefix for all custom identifiers: `mumakan_`.

---

## 1. Character flags (`add_character_flag` / `has_character_flag`)
| Flag | Meaning |
|---|---|
| `has_forged_minor_ring` | Palier 1 complete; Minor Ring forged. Unlocks legacy track + Palier 2. |
| `has_forged_master_ring` | Palier 2 complete (either outcome). Unlocks Palier 3. |
| `mumakan_master_ring_awakened` | Palier 2 outcome B (full success, Akhâsh-Mûmak Éveillé). |
| `mumakan_master_ring_imperfect` | Palier 2 outcome A (imperfect Elven-tier ring). |
| `mumakan_is_master_ring_bearer` | Current holder of the Shadow Sovereignty chain (transfers on succession). |
| `mumakan_ringbound_servant` | A vassal who has received a Ring of Loyalty. |

## 2. Global / local variables
| Variable | Scope | Meaning |
|---|---|---|
| `mumakan_loyalty_rings_forged` | character variable on the master-ring bearer | Counter, hard cap 16. |
| `mumakan_master_bearer` | global variable | Points to the current master-ring bearer character (for tribute/faction logic). |

> The 16-ring cap is tracked **per chain** as a character variable on the bearer and transferred
> on succession, so a new campaign / new bearer is not penalized by a stale global. (See §9.)

## 3. Traits (`common/traits/`)
| ID | FR display | Type | Key mechanics |
|---|---|---|---|
| `loyalty_to_the_ring` | Serviteur de l'Anneau | personality, inheritable (100%) | +5 Prouesse, +2 Érudition. Faction immunity (via on_action, §8). AI loyalty. |
| `will_of_the_ring` | Volonté de l'Akhâsh-Mûmak | personality, inheritable (100% to primary heir) | +3 Martial, +3 Intrigue, +15% dread gain, −10 general opinion, +10% monthly stress. |
| `mumakan_ringbound_immortal` | (hidden) | `immortal = yes` | Granted alongside `loyalty_to_the_ring` and to the awakened bearer. Stops aging. |

## 4. Building (`common/buildings/`) — duchy building (`type = duchy_capital`)
- ID: `mumakan_forges_maitresses` (3 levels; modifiers scale; decisions gate at level 1).
- FR display: **Forges Maîtresses de Mûmakan**.
- `can_construct`: capital holding inside Far Harad region, ruler Feudal **or** Clan government.
- Modifiers (per level, full at L3): MAA Mûmakil damage +30%, MAA Mûmakil toughness +30%,
  artifact creation time −20%, monthly piety +10%, monthly prestige +10%.
- MAA scaling restricted to Mûmakil-type regiments via a custom MAA modifier where supported,
  else applied to the owner's MAA generally (documented).

## 5. Decisions & interactions
| ID | Kind | FR title | Cost | Gate |
|---|---|---|---|---|
| `mumakan_decision_minor_ring` | major decision (player-only) | Plonger dans le Savoir des Anneaux | 20 000 piety + 5 000 gold | building L1 in capital, eligible ruler, `NOT has_forged_minor_ring` |
| `mumakan_decision_master_ring` | major decision (player-only) | Forger l'Anneau Maître | 100 000 piety **OR** 100 000 prestige | `has_forged_minor_ring`, `NOT has_forged_master_ring` |
| `mumakan_interaction_loyalty_ring` | character interaction (target vassal) | Offrir un Anneau de Loyauté | 40 000 piety | `has_forged_master_ring`, counter < 16, target is internal King/Duke vassal |

> Palier 3 uses a **character interaction** (right-click a vassal) rather than a decision because
> it targets another character — the natural, lightweight CK3 pattern, no custom UI. Honors the
> client's "decision/event-driven, no custom windows" constraint.

## 6. Artifacts (`common/artifacts/...`)
All rings equip into an existing vanilla inventory slot (no custom slot needed) and reuse an
existing vanilla ring/regalia **visual** so the mod ships zero missing textures.
| Template | FR name | Modifiers (while owned) |
|---|---|---|
| `mumakan_minor_ring` | L'Anneau Mineur | +2 Martial, +5 vassal opinion, +5% knight (MAA) effectiveness |
| `mumakan_elven_ring` | Anneau Elfique Imparfait | +5 Learning, +5 Intrigue, −50% hostile scheme resistance |
| `mumakan_master_ring` | L'Akhâsh-Mûmak Éveillé | +10 Martial, +10 Prowess, +5 Intrigue, +health; dread/longevity via effects |
| `mumakan_loyalty_ring` | Anneau de Loyauté | cosmetic; mechanics carried by `loyalty_to_the_ring` trait |

## 7. Opinion modifier (`common/opinion_modifiers/`)
- `mumakan_ring_of_loyalty_opinion`: +100, `locked = yes` (cannot decay), toward liege.

## 8. On-actions & scripted effects (`common/on_action/`, `common/scripted_effects/`)
| Effect / hook | Purpose |
|---|---|
| `mumakan_forge_minor_ring_effect` | Palier 1: flag + create Minor Ring artifact + unlock legacy + bearer var. |
| `mumakan_forge_master_ring_effect` | Palier 2: fire RNG event `mumakan_ring.0001`. |
| `mumakan_master_ring_awakened_effect` | Outcome B: artifact, lock dread 100, immortality, +50% fertility, will_of_the_ring on heir. |
| `mumakan_master_ring_imperfect_effect` | Outcome A: Elven ring artifact + Mordor threat hooks. |
| `mumakan_grant_loyalty_ring_effect` | Palier 3: trait, faith convert, +100 opinion, immortality, +30% fertility, artifact, counter++. |
| `mumakan_transfer_bearer_status` (on_death/succession) | Move bearer flag/var + master ring to primary heir; grant `will_of_the_ring`. |
| `mumakan_monthly_tribute` (legacy T5, monthly pulse) | Each ring-bound vassal pays 5% monthly gold to bearer. |
| `mumakan_lock_dread` (yearly pulse) | Re-set awakened bearer dread to 100 (no native lock). |
| `mumakan_faction_immunity` (faction on_actions + monthly cleanup) | Eject `loyalty_to_the_ring` holders from factions vs the bearer. |
| `mumakan_check_cadet_scission` (yearly pulse) | If an immortal servant's house > 15 living members, split a cadet branch. |

## 9. CK3 engine-limitation decisions (documented for the client)
1. **No "lock dread" primitive** → maintained by re-setting `set_dread = 100` on a yearly pulse for
   the awakened bearer (+ the trait removes decay via `dread_loss_mult`). Functionally locked.
2. **No per-character "block factions" primitive** → enforced via faction on_actions + a monthly
   cleanup pulse that removes `loyalty_to_the_ring` holders from any faction targeting the bearer.
   Robust and override-free.
3. **Immortality / stop aging** → a hidden `immortal = yes` trait (`mumakan_ringbound_immortal`).
4. **No `common/defines/` overrides** → deliberate. RoE heavily edits defines; overriding them
   would risk conflicts and checksum drift. All numeric tuning is done in script values instead.
5. **16-ring cap** tracked as a character variable on the bearer (transferred on succession), not a
   single global, so it is correct across succession and never blocks a fresh campaign.
6. **Player-only** crafting enforced via `is_ai = no` in decision `is_shown`/`is_valid`; AI never
   sees Paliers 1–3 (multiplayer-safe, prevents AI desync from heavy automated logic).

## 10. Localization — FR display strings (used in `english/` AND `french/` so names show in any UI language)
See `localization/` files. Master list of human-facing strings:
- Forges Maîtresses de Mûmakan; Dégâts des Hommes d'Armes Mûmakil; Robustesse des Hommes d'Armes Mûmakil
- Plonger dans le Savoir des Anneaux; L'Anneau Mineur; Forger l'Anneau Maître; Akhâsh-Mûmak (Saurgûl)
- Le Chef-d'Œuvre Imparfait; L'Akhâsh-Mûmak Éveillé; Les Anneaux de Loyauté; Offrir un Anneau de Loyauté
- Serviteur de l'Anneau; Volonté de l'Akhâsh-Mûmak; La Volonté des Anneaux (legacy)
- Legacy tiers: Maîtrise du Savoir des Anneaux; Majesté Absolue; Protocoles d'Assujettissement; Garde des Serviteurs; Tribut des Sans-Âme
