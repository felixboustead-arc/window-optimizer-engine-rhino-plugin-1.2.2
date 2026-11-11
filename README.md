# Active Light Optimizer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Rhino 8](https://img.shields.io/badge/Rhino-8-informational)
![Release 1.2.2](https://img.shields.io/badge/release-1.2.2-blue)

Active Light Optimizer (ALO) is a Grasshopper plugin for Rhino 8. It plans how a moving, motorised window shade should be set through the day.

The window it is built around has a tube that tilts and shading blades that open and close. For each window and each moment of the day, ALO works out the tube angle and the blade setting. A slider sets the balance: at 1 it plans only for the light, keeping the direct sun where you want it. Below 1 it also weighs thermal comfort in a reference room.

It also includes a preview set of nodes that estimate the air temperature of every room of a whole building over a year.

## What it does

- **Active Light Optimizer node.** Give it 1 to 15 windows (as Planes) and a clock time. For each window it gives back a ring angle in degrees and an aperture in percent closed.
- **Your own mechanism.** Five nodes describe your own moving window: ALO Parameter, ALO Joint, ALO Body, ALO Room Target and ALO Scene. Two movement parameters and hinged joints are supported.
- **Whole-building temperatures (preview).** Four nodes give the hourly air temperature, hours above 26 °C and below 18 °C, and optional heating and cooling loads for every room: ALO Material, ALO Construction, ALO Zone and ALO Building Thermal. The planner's moving shading can feed into them.
- **Rhino stays usable.** The calculation runs in the background in a separate Python process. Plans are cached, so after the first run a new clock time answers at once.

## Requirements

- Windows 10 or 11
- Rhino 8 with Grasshopper
- CPython 3.9 or newer from [python.org](https://www.python.org/downloads/windows/). No extra packages are needed.
- An EnergyPlus weather file (`.epw`) for your site. One for Williamtown (Newcastle, NSW) is included as an option.

## Install

1. **Download** `ActiveLightOptimizer-1.2.2.yak` and `ActiveLightOptimizer-1.2.2-example.zip` from the [Releases](../../releases) page.
2. **Install the plugin.** Drag the `.yak` file into the Rhino 8 window, or run `yak install ActiveLightOptimizer-1.2.2.yak` in a command window. Restart Rhino.
3. **Point it at Python.** Set the Windows environment variable `ALO_PYTHON` to the full path of your `python.exe`. Alternatively, create `%LOCALAPPDATA%\ActiveLightOptimizer\engine_config.json` containing:
   ```json
   {"python": "C:/path/to/python.exe", "epw": "C:/path/to/weather.epw"}
   ```
   If Python 3.9+ is already on your PATH you can skip this.
4. **Set a weather file.** ALO does not pick one for you. Set `epw` in that config file, set the variable `ALO_EPW`, or wire a path into the node's `epw` input.
5. **Check the install.** The node's `status` output ends with `| engine: <folder>`. Open that folder and run `check_install.cmd`. Every line should say PASS.

The engine is bundled inside the plugin. On the first run it unpacks itself to `%LOCALAPPDATA%\ActiveLightOptimizer\engine`. You never unpack anything by hand.

## Quick start

1. Unzip the example. Open `ActiveLightOptimizer_house.3dm` in Rhino, then `ActiveLightOptimizer_house.gh` in Grasshopper.
2. Turn `run planner` on and leave the slider at 1.
3. While it plans, `status` reads "planning in background". When it reads "planned", the ring and aperture lists fill in. The first run is the slow one.
4. Change the clock. The example opens on 21 December, when only the west windows move. Set a June date to see the north blades move.

The example is a small, neutral two-storey house. It has fixed shading fins and four north windows whose tilting panel and louvre blades the planner optimises.

## Documentation

| File | What's in it |
|---|---|
| [USER_GUIDE.md](USER_GUIDE.md) | Every input and output, settings, your own mechanism, the building recipe, troubleshooting |
| [LIMITS.md](LIMITS.md) | What the tool does not do and how far to trust its numbers. Please read it before you use a result. |
| [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) | Credits for EnergyPlus, weather data and published methods |
| [engine/README.md](engine/README.md) | The package README: engine lookup order, environment variables, where files are written |

## How far to trust it

The window planner is the main tool. The whole-building temperature nodes are a **preview**. They have been compared with EnergyPlus on a limited set of cases and have not passed a final validation gate. When the planner's moving shading drives the temperatures, the effect is an estimate. It was compared with EnergyPlus for the default window only, and came out up to about 2.3 times the EnergyPlus effect. The default constructions are typical values. Details are in `LIMITS.md`, section 11.

## Repository layout

```
README.md                 this file
LICENSE                   MIT licence
THIRD_PARTY_NOTICES.md    third-party credits
USER_GUIDE.md             full user guide
LIMITS.md                 limits and accuracy
example/                  neutral example house (.3dm + .gh)
grasshopper_nodes/        source of the ten Grasshopper nodes (Python scripts + component definitions)
engine/                   the engine source as shipped inside the plugin (Python), with its own README,
                          install check, reference data and the optional Williamtown weather file
```

The `.yak` on the Releases page contains the Grasshopper nodes (`.gha`, built from `grasshopper_nodes/`) and this engine as one zip. `engine/MANIFEST.sha256` lists the hash of every engine file. How to build the plugin yourself is described in `grasshopper_nodes/README.md`.

## What's new in 1.2.2

- Clearer error messages for bad inputs: the message names the field.
- Warnings for zones smaller than about 2 m and for odd shading plates.
- Negative internal gains are refused.
- The planner's cached solar factors now follow the content of the weather file, so the first run after upgrading recomputes them.
- The example is the house example only.

Earlier releases: 1.2.1 let the planner's moving shading drive the whole-building temperatures (optional `active_shading` input) and added an air-change input. 1.2.0 added the four whole-building temperature nodes.

## Licence

Active Light Optimizer is released under the [MIT License](LICENSE). Copyright (c) 2026 Felix Boustead.

Third-party data and methods it uses, including EnergyPlus reference output, the Climate.OneBuilding.Org weather file and published comfort and calculation methods, are credited in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

## Author

Felix Boustead. Questions and bug reports are welcome on this repository's Issues page.
