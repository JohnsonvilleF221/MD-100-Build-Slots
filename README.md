# MD 100 Build Slots

A submod for **Millennium Dawn** that raises building slots on high-population states: the
population-driven base ramps **linearly up to 85 slots**, and the engine ceiling is lifted to
**100** so state modifiers (railways, infrastructure, etc.) can stack on top of the 85 base up to
a hard max of 100.

## What it does

MD assigns every state a `state_category` from its population (`state_init_setup` in
`common/scripted_effects/00_economic_effects.txt`), and each category's `local_building_slots`
sets that state's shared building-slot base. Five files do the work:

- `common/state_category/state_09.txt … state_18.txt` — the ramped `local_building_slots` values.
  (`state_00`–`state_08`, `state_inhospitable`, `state_military_base` are included unchanged so the
  submod can `replace_path="common/state_category"` and win the override deterministically.)
- `common/defines/zzz_build_slots_defines.lua` — raises `NDefines.NBuildings.MAX_SHARED_SLOTS`
  from MD's **56** to **100**. This is the engine ceiling on total factory-shared slots; without it
  the base is clamped to 56. The `zzz_` prefix is required so this file loads **after** MD's
  `MD_defines.lua` (defines execute in alphabetical order; last assignment wins).
- `interface/countrystateview.gui` — copy of MD's state view with the shared-slot grid retuned to
  **12 columns at 29×25px** so up to 100 slots fit inside the panel instead of overflowing it.
- `interface/md_100_build_slots.gfx` — defines `GFX_damage_bar_slim` (**24×3**, vs MD's 32×6). Purely
  additive: a new sprite name, so MD's `GFX_damage_bar` is unchanged for province entries and
  landmarks. Note the damage bar only draws on *damaged* buildings, so this is invisible most of
  the time — it was not the cause of the slot crowding (see below).
- `interface/MD_energy_scripted.gui` — copy of MD's file with the **"Renewable Energy Hotspot"**
  readout shrunk so it stops colliding with the denser slot grid: font `hoi_20bs` → `hoi_16mbs`
  (the smallest font HOI4 defines), and the ⚡ icon scaled to `0.7` (32px → 22px). Both are
  offset down-and-right (icon `135,316` → `187,325`; text `162,321` → `210,328`) to clear the
  bottom row of slots. Everything else in the file is byte-identical to MD's.

### Why the slots overlapped

The grid area is fixed: the `shared_slot_building_bg` panel is 440px wide at x=8 (so the grid runs
x=100→448, **348px**) and y=320→564 with the grid starting at y=335 (**229px**). At 12 columns and
9 rows that is exactly 348/12 = **29px** and 229/9 ≈ **25px** per cell — there is no spare room in
either axis.

But `state_shared_slot_building_entry` draws sprites at their native sizes, which are all *larger*
than that cell:

| Element | Native | Drawn before | vs 29×25 cell |
| ------- | ------ | ------------ | ------------- |
| `build_slot_bg` | 39×32 | 39×32 (unscaled) | **+10 / +7 overlap** |
| `building_picture` | 46×46 | 28.1 (scale 0.61) | +3 vertical overlap |
| `building_status_overlay` | 46×46 | **46×46 (unscaled)** | **+17 / +21 overlap** |

The unscaled status overlay was by far the worst offender. Each entry is a 70×70 container with
`clipping = no`, so all of this spilled over the neighbouring cells. The fix scales the entry
contents down to the cell: bg `0.74` → 28.9×23.7, picture and overlay `0.54` → 24.8×24.8, remove
button `0.74`, and the damage bar repositioned to `x=3 y=20`.

The icon is slightly smaller than before (24.8 vs 28.1) — in a 29×25 cell you cannot have a 28px
icon *and* no overlap. To trade back the other way, raise `building_picture` `scale` and accept
some overlap.

Ramp (base `local_building_slots`, even ~+8/band, capping at 85; nothing below `state_09` changes):

| Category | Population band | MD base | This submod |
| -------- | -------------- | ------- | ----------- |
| state_08 | 8.5m – 10.5m   | 22      | 22 (as-is)  |
| state_09 | 10.5m – 13m    | 25      | 30          |
| state_10 | 13m – 16m      | 28      | 38          |
| state_11 | 16m – 19m      | 31      | 46          |
| state_12 | 19m – 23m      | 33      | 54          |
| state_13 | 23m – 27m      | 36      | 61          |
| state_14 | 27m – 34m      | 39      | 69          |
| state_15 | 34m – 42m      | 42      | 77          |
| state_16 | 42m – 52m      | 44      | **85**      |
| state_17 | 52m – 65m      | 47      | 85          |
| state_18 | >65m           | 50      | 85          |

50m population lands in the `state_16` band (42m–52m), where the base caps at 85. With the +10%
railway/infrastructure modifiers a top state computes ~93 and can climb toward the 100 engine cap.
To change the base ceiling, edit `local_building_slots` in the relevant `state_XX.txt`; to change
the hard max, edit `MAX_SHARED_SLOTS` in `zzz_build_slots_defines.lua`.

## Install

1. Copy the whole **`MD 100 Build Slots`** folder into your HOI4 mod directory:
   `Documents/Paradox Interactive/Hearts of Iron IV/mod/`
2. Copy **`MD 100 Build Slots.mod`** into that same `mod/` directory (next to the folder, not inside it).
3. In the launcher, enable a playset with **Millennium Dawn first** and **MD 100 Build Slots last**
   (it must load after MD). The `dependencies` field lists MD's Beta Test / Modern Day / Developer
   names so the launcher enforces this automatically; if you run a differently-named MD build, add
   its exact name to `dependencies` in both `.mod` files.

Defines and interface changes require a **full game restart** (not just a save reload). Slot-count
changes apply live to existing saves once loaded.

## Compatibility note

The submod ships two full-file copies of MD interface files: `countrystateview.gui` and
`MD_energy_scripted.gui`. Both must be re-synced if MD changes them. Note they are pinned to
**different MD revisions on purpose** — see below.

`interface/countrystateview.gui` differs from MD in two places: the shared-slot grid
(`slotsize` / `max_slots_horizontal`, lines 153-154) and the `state_shared_slot_building_entry`
contents rescaled to fit the cell (see "Why the slots overlapped" above).

**This file is deliberately pinned to MD `main` @ `8906fde4` (2026-07-04)** — the build the submod
was play-tested and stabilised against. It is *not* tracking MD's current `main`. As of MD
`060d405415` (2026-09-02) that means it also holds July values for four lines MD has since changed
in its "Protests ui rework + ui fixes" commit (`5ddb22dfb9`): the `dynamic_modifiers_grid` position
and slotsize, and the positions of `province_building_special_icon_entry` and
`state_view_dynamic_modifier_entry`. Those are cosmetic state-view positions, not slot logic.

To re-sync later: copy MD's current `countrystateview.gui` over this one, re-apply the changed
lines above, and **re-test in game before release** — a clean re-sync leaves `diff` against MD
showing exactly the changes listed above.

### `MD_energy_scripted.gui` tracks current MD, not the July pin

Unlike `countrystateview.gui`, this copy is based on MD `060d405415` (2026-09-02) — the revision
the live Workshop build of MD 2.0.0 ships, verified byte-identical. That is deliberate: MD changed
this file after the July pin, adding the **Automation energy policy** checkbox and label
(20 lines). Basing the copy on the July revision would have silently deleted a working MD feature
from the energy UI. Since this override is new and has no play-test history tied to the older
revision, matching what the game actually runs is the safer base.
