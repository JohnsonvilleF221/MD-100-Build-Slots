# MD 100 Build Slots

A **Millennium Dawn** submod that raises building slots on high-population states. The base ramps
up to **85**, and the engine cap is lifted to **100** so railway and infrastructure modifiers have
somewhere to go.

## What it changes

MD gives every state a category based on its population, and that category sets the state's
building-slot base. This mod retunes the top ten categories:

| Population    | MD | This mod    |
| ------------- | -- | ----------- |
| 8.5m – 10.5m  | 22 | 22 (unchanged) |
| 10.5m – 13m   | 25 | 30          |
| 13m – 16m     | 28 | 38          |
| 16m – 19m     | 31 | 46          |
| 19m – 23m     | 33 | 54          |
| 23m – 27m     | 36 | 61          |
| 27m – 34m     | 39 | 69          |
| 34m – 42m     | 42 | 77          |
| 42m – 52m     | 44 | **85**      |
| 52m – 65m     | 47 | 85          |
| 65m+          | 50 | 85          |

Anything below 10.5m population is left alone — this is aimed at the states that were being
squeezed, like China, India and Japan, not a blanket buff. It affects 146 of MD's 1250 states.

A top state with the usual +10% railway and infrastructure modifiers lands around 93 and can climb
toward the 100 cap.

## Install

1. Copy the **`MD 100 Build Slots`** folder into `Documents/Paradox Interactive/Hearts of Iron IV/mod/`
2. Copy **`MD 100 Build Slots.mod`** into that same `mod/` folder — beside the folder, not inside it
3. In the launcher, enable a playset with **Millennium Dawn first and this mod last**

Restart the game fully after enabling; some changes are only read at startup. Existing saves pick
up the new slot counts as soon as they load.

## Compatibility

Built for HOI4 **1.19.x** and Millennium Dawn **2.0**. It replaces MD's building slot categories
and two of its interface files, so it will clash with any other mod that changes building slots or
the state screen.

---

## Notes for maintainers

Five files do the work:

| File | What it does |
| ---- | ------------ |
| `common/state_category/state_09–18.txt` | The ramp values. `state_00`–`state_08` and the two special categories are shipped unchanged so the mod can `replace_path` the folder and win the override cleanly. |
| `common/defines/zzz_build_slots_defines.lua` | `MAX_SHARED_SLOTS` 56 → 100. The `zzz_` prefix makes it load after MD's `MD_defines.lua` — defines run in alphabetical order and the last assignment wins. |
| `interface/countrystateview.gui` | Grid retuned to 12 columns at 29×25px, and the slot entry contents rescaled to fit that cell so icons don't overlap. |
| `interface/md_100_build_slots.gfx` | Defines `GFX_damage_bar_slim`. Purely additive — a new sprite name, so MD's own damage bar is untouched. |
| `interface/MD_energy_scripted.gui` | "Renewable Energy Hotspot" readout shrunk and moved so it clears the denser grid. |

To retune: edit `local_building_slots` in the `state_XX.txt` files for the ramp, and
`MAX_SHARED_SLOTS` for the hard cap.

### The two `.gui` files are full copies of MD's

HOI4 has no way to patch a single line of a `.gui` — interface files are replaced by filename, so
changing one property means shipping the whole file. `countrystateview.gui` is 10,048 lines to
change 11 of them.

**If MD updates either file, this mod will silently revert their changes.** Re-copy MD's version,
re-apply the handful of changed lines, and re-test in game before releasing.

The two files sit on different MD revisions on purpose:

- `countrystateview.gui` is pinned to MD `8906fde4` — the build this was play-tested against.
- `MD_energy_scripted.gui` tracks current MD, because MD added an Automation energy policy
  checkbox to it after that date and pinning would have deleted it.
