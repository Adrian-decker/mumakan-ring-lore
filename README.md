# Mûmakan Ring-Lore & Shadow Sovereignty

A functional, mechanics-driven **sub-mod for Realms in Exile v4.3** (CK3 base 1.19.x). It lets any
eligible Southern or Black Númenórean ruler of the Far Harad / Mûmakil south break from Sauron's
authority and forge their own path of Shadow: independent ring-crafting, Mûmakil military
centralisation, and imperial domination — all delivered through decisions, events and a dynasty
legacy, with French in-game nomenclature.

> **Standalone.** Adds only new files. Does **not** overwrite Realms in Exile or base-game content,
> and makes **no `defines` edits**. Requires Realms in Exile enabled, loaded above this sub-mod.

## Repository layout
```
mod/
  mumakan_ring_lore.mod          # launcher descriptor (path = mod/mumakan_ring_lore)
  mumakan_ring_lore/
    descriptor.mod               # in-folder descriptor (no path key)
    thumbnail.png                # Workshop preview (placeholder; replace before publishing)
    common/                      # buildings, decisions, interactions, traits, artifacts,
                                 #   modifiers, opinion_modifiers, on_action, dynasty_legacies,
                                 #   dynasty_perks, scripted_effects/triggers, script_values
    events/                      # mumakan_ring_events.txt
    localization/
      english/mumakan_l_english.yml   # French names + English descriptions (UTF-8 + BOM)
      french/mumakan_l_french.yml     # French names + French descriptions (UTF-8 + BOM)
CHANGELOG.md                     # feature changelog
SETUP.md                         # install, load order, mechanics guide, limitations, test checklist
DESIGN_SPEC.md                   # internal identifier registry + engineering decisions
CONFIRMED_REFERENCE.md           # identifiers/syntax verified against RoE's live source
```

## Quick start
See **[SETUP.md](SETUP.md)** for installation, load order, the full progression walk-through, the
strict tier hierarchy, the hard 16-ring cap, and the documented engine-limitation handling.

## Core systems
| System | Where |
|---|---|
| Forges Maîtresses de Mûmakan (duchy building) | `common/buildings/mumakan_forges_buildings.txt` |
| Palier 1 & 2 decisions | `common/decisions/mumakan_decisions.txt` |
| Palier 3 vassal interaction | `common/character_interactions/mumakan_loyalty_ring_interaction.txt` |
| Ring artifacts (4 templates) | `common/artifacts/templates/mumakan_ring_templates.txt` + `common/modifiers/` |
| Traits | `common/traits/mumakan_traits.txt` |
| Master-ring RNG, Mordor threat, tribute | `events/mumakan_ring_events.txt` |
| Succession / faction immunity / scission | `common/on_action/mumakan_on_actions.txt` + `common/scripted_effects/` |
| Dynasty legacy (La Volonté des Anneaux) | `common/dynasty_legacies/` + `common/dynasty_perks/` |

All identifiers were verified against the live Realms in Exile source; see `CONFIRMED_REFERENCE.md`.
