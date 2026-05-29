# Confirmed Reference — verified against RoE v4.3 source (github.com/CK3RealmsinExile/RealmsInExile @ master)

Everything below was read directly from RoE's live source files, not guessed. Authoring relies on it.

## Base / descriptor
- RoE v4.3 → CK3 base **1.19.x** → `supported_version="1.19.*"`. Localization must be **UTF-8 with BOM**; files end `_l_english.yml` / `_l_french.yml`.

## RoE identifiers we depend on (mod requires RoE active)
- Culture gate: `culture = { has_cultural_pillar = heritage_mumakani }` (Mûmakan/Mûmakil). Black Númenórean = `heritage_southern_adunaic`. Group param = `heritage_group_mumakani`.
- Geographic regions: `middleearth_harad`, `middleearth_harad_far`, `middleearth_uttersouth_mumakan` (usable via `is_in_region` / building `region`).
- Governments retained from vanilla: `feudal_government`, `clan_government`, `tribal_government`.
- Men-at-arms: Mûmakil type id = `war_mumak` (base `type = elephant_cavalry`). **RoE buffs them via `elephant_cavalry_damage_mult` / `elephant_cavalry_toughness_mult` / `elephant_cavalry_max_size_add`** (confirmed in `innovation_war_mumak`). We use the same keys.
- Reusable RoE scripted triggers: `can_recruit_mumakil_trigger` (character), `sauron_is_alive` (global), `building_requirement_castle_city_church = { LEVEL = 01 }`, `building_requirement_tribal = yes/no`.
- Ring artifact VISUAL ids (reuse → zero textures): `one_ring`, `nenya`, `vilya`, `narya`, `ring_of_men`, plus dwarven/nazgûl rings. We use: minor=`narya`, elven-imperfect=`nenya`, master-awakened=`vilya`, loyalty=`ring_of_men`.
- Building icon (reuse): `"icon_building_hall_of_heroes.dds"` (confirmed present in RoE haradrim buildings).

## Confirmed CK3 syntax patterns (from RoE working files)
- **Duchy building** (`type = duchy_capital`): top-level `cost_gold` / `cost_prestige` / `construction_time = slow_construction_time`; modifier block is `character_modifier = { }` (SINGULAR) and `duchy_capital_county_modifier = { }`; `can_construct_potential` / `is_enabled` (scope is province; use `culture`, `county.holder`, `building_requirement_*`); `show_disabled = yes`; `flag = fully_upgraded_duchy_capital_building`; `ai_value`; `type_icon`.
- **Artifact create** (runtime): `create_artifact = { name=… description=… creator=… type = miscellaneous visuals=<ring> wealth=100 quality=100 template=<tmpl> modifier=<static modifier> save_scope_as = newly_created_artifact decaying = no }`; then `scope:newly_created_artifact = { equip_artifact_to_owner_replace = yes }`. Bonuses live in the `modifier =` (a static modifier in `common/modifiers/`); the **template** holds `can_equip` / `can_benefit` / `fallback` / `unique`.
- **Decision**: `picture`, `desc`, `is_shown` (`is_ai = no` = player-only), `is_valid`, `cost = { gold piety prestige }`, `effect`, `ai_check_interval`, `ai_potential`, `ai_will_do`. Title loc key = decision id.
- **Character interaction** (character target): `category = interaction_category_friendly`, `desc`, `is_shown` (scope:actor / scope:recipient), `is_valid_showing_failures_only`, `auto_accept = yes`, `on_accept = { }`, `ai_*`.
- **Dynasty legacy track**: `track = { is_shown = { dynasty = { OR = { … has_dynasty_perk = <perk_1> } } } }`. **Perk**: `{ legacy = <track> can_be_picked = { … } character_modifier = { name = <loc> … } effect = { } ai_chance = { value = N } }`.
- **Confirmed effects/triggers**: `add_dread` (no set_dread), `monthly_dread`/`dread_baseline_add`/`dread_loss_mult` (modifiers), `create_cadet_branch = yes`, `any_house_member = { count >= N … }`, `leave_faction = yes` + `is_a_faction_member`, `on_death` exposes `primary_heir` event target, `set_character_faith = scope:x.faith`, `add_opinion = { target modifier }`, `add_character_modifier`, `trigger_event = { id days = 30 }`, dynasty/character variables. No monthly on_action pulse → monthly tribute uses a self-scheduling event.
- Confirmed trait modifier keys: `martial prowess intrigue learning diplomacy stewardship health fertility general_opinion vassal_opinion dread_baseline_add dread_gain_mult dread_loss_mult monthly_dread stress_gain_mult knight_effectiveness_mult monthly_piety monthly_prestige immortal inherit_chance`.

## Deliberate engine-limit handling (see SETUP.md §Limitations)
- "Lock Dread 100" → `add_dread = 100` on forge + character_modifier `monthly_dread = 100` (caps at 100) + `dread_loss_mult` on trait. Functionally pinned.
- Immortality → hidden trait `mumakan_ringbound_immortal { immortal = yes }`.
- Faction immunity → `on_yearly_pulse` cleanup ejecting `loyalty_to_the_ring` holders from factions targeting the bearer (`leave_faction = yes`), no base-file override.
- No `common/defines/` overrides (RoE-compat). Player-only crafting via `is_ai = no` (MP-safe).
