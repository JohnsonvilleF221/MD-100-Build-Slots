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
squeezed, like China, India and Japan, not a blanket buff. It affects roughly 146 of MD's 1250 states (from game start).

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
