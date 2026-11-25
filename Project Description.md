# Sylphon RnD Project Overview

> This document describes the mod's structure for contributors and AI assistants. The original development environment used **0.98a-RC8** at `C:\game\StarSector.v0.9.8a-RC8`; `v0.9.8a` in the directory name is an older naming convention.
> Dependencies: **LazyLib and MagicLib are required**. GraphicsLib (`shaderLib`) optionally provides dynamic lighting/materials; Nexerelin optionally provides random-sector and faction features. IndEvo, Vayra's Sector, and Commission Bonus have optional integrations.

## Features

Sylphon RnD is a faction mod centered on nullspace technology. It adds purple-and-black ships, weapons, and fighters; 28 hullmods; 19 ship systems; 1 faction; 2 handcrafted systems, Nym and Rofocale; a Sylph Core installation submarket; bar story events; and 4 unique bounties.

**Version history:** **1.2.2 overhauled translation quality**, rewriting descriptions.csv from upstream English, removing machine-translation spacing, standardizing punctuation, researching Ar tonelico/Hymmnos terms such as Replekia, Tilia, Eolia, Infel, Ravana, and Phira, and rewriting hullmod descriptions while fixing 4 defects. **1.1** rebuilt the jar for the 0.98a API and fixed issues including an unregistered bar event. **1.2 completed Chinese localization**, covering 202 data entries and 215 program-text constants in `jars\src`, rebuilding with `build.ps1`, and fixing descriptions.csv quoting. **1.2.1 fixed the codex NPE** for Integrated Nullspace Conduits: codex2 passes `ship=null`, which SRD_NullspaceConduits previously dereferenced. A null guard was added, and the other 6 implementations were audited. Details are in `changelog.txt` and `ai\README_汉化说明.md`.

## Basic Information (mod_info.json)

- id：`Sylphon_RnD`
- Name: Sylphon RnD
- Version: **1.2.2**, matching `modVersion` 1.2.2 in `sylphon.version`
- gameVersion：`0.98a-RC8`
- Dependencies: `lw_lazylib` (LazyLib), `MagicLib` (MagicLib)
- modPlugin：`data.scripts.SRD_ModPlugin`
- Jar: `jars/SylphonRnD.jar`, built from `jars/src`. The original development copy backed up version 1.0 as `jars/SylphonRnD.jar.orig`.

## Directory Structure

```
mods/sylphon/
├── mod_info.json                  # Mod metadata: ID, name, version, gameVersion, dependencies, plugin
├── sylphon.version                # Version Checker metadata
├── changelog.txt                  # Update history, including the codex fix
├── 项目说明.md                    # This document
├── ai/                            # Contributor/AI workspace included with the delivery
│   ├── skills/                    # Development skills: migration, delivery, compilation, rendering, generation
│   │                              # and localization: starsector-mod-localization, -content, engine-repro, dialogue
│   ├── en/  zh/                   # Aligned localization corpus: 460 English originals and Chinese translations
│   │                              # + zh\已汉化_无英文原文.json: 593 historical entries without English originals
│   ├── 术语表.json                # Glossary, core-localization research, and formatting rules
│   ├── README_汉化说明.md         # Localization scope, fixes, validation, reproduction, and rollback details
│   ├── 脚本/                      # Build/validation scripts: build.ps1, tools/*.js, verify/LoadTest.java
│   │   └── 汉化/                  # Localization toolchain: 54 scripts/recipes, including csvlib/pseudojson
│   └── 报告/                      # 0.98a migration and localization reports, reviews, and structural analysis
├── data/
│   ├── scripts/                   # Runtime code: Janino scripts, mission definitions, SRD_ModPlugin, etc.
│   │   └── SRD_ModPlugin.java     # modPlugin：onApplicationLoad / onNewGame / onGameLoad
│   ├── config/                    # settings.json: plugin registration, designTypeColors, sprite names
│   │                              # custom_entities.json / engine_styles.json / hull_styles.json
│   │                              # sounds.json / exerelin*（Nexerelin）/ indEvo / prism / vayraBounties
│   ├── hullmods/hull_mods.csv     # Hullmod definitions: name, id, script, desc, short, sprite, etc.
│   ├── hulls/                     # ship_data.csv, wing_data.csv, *.ship hull geometry, and skins/
│   ├── weapons/                   # weapon_data.csv, *.wpn weapons, and proj/*.proj projectiles
│   ├── shipsystems/               # ship_systems.csv and *.system, including statsScript/aiScript
│   ├── variants/                  # *.variant loadouts, including fighters/
│   ├── campaign/                  # industries.csv / submarkets.csv / special_items.csv / rules.csv
│   ├── lights/                    # GraphicsLib lighting/material data: SRD_lights.csv, SRD_texture.csv
│   ├── missions/                  # descriptor.json and MissionDefinition.java
│   ├── strings/                   # Visible text: descriptions.csv and tips.json
│   ├── trails/trail_data.csv      # Legacy MagicLib projectile trails, still loaded
│   └── world/factions/            # sylphon.faction / default_ship_roles.json / factions.csv
├── graphics/                      # Ship, weapon, icon, background, portrait, and effect textures
├── sounds/                        # Sound effects and 2 combat tracks in sounds/sylphon/**
└── jars/
    ├── SylphonRnD.jar             # Runtime jar rebuilt from source for the compatibility/localization releases
    ├── SylphonRnD.jar.orig        # Original 1.0 backup in the development checkout, for comparison/rollback
    └── src/                       # 126 Java source files; UI text localized since 1.2; excluded from delivery zip
```

## Configuration (User Editable)

- `data/config/settings.json`: `plugins` registers 6 combat plugins; `designTypeColors` defines Sylphon RnD and Sylphon Outcast colors; `graphics` maps sprite names to textures.
  `designTypeColors` keys must exactly match the `tech/manufacturer` values in hull_mods.csv, weapon_data.csv, and ship_data.csv for faction colors to work.
- `data/hullmods/hull_mods.csv`, `data/hulls/ship_data.csv`, `data/weapons/weapon_data.csv`, and `data/shipsystems/ship_systems.csv` are read by column name. Missing columns use defaults, and newly added columns may remain empty.
- `data/config/exerelinFactionConfig/sylphon.json` defines Nexerelin starting relationships, fleets, diplomatic tendencies, and other faction settings.
- `data/config/exerelin/corvus_capitals.csv` and `corvus_spawnpoints.csv` reference systems by human-readable names. They must match Nym and Rofocale in the `createStarSystem()` calls of `SRD_Nym.java` and `SRD_Rofocale.java`.
- `data/world/factions/sylphon.faction` defines faction colors, known ship/weapon tags, fleet composition, music, and ranks.
- Numeric data changes do not require jar recompilation. Changes to `jars/src/**/*.java` do.

## Common Operations

| Operation | Method |
|---|---|
| Enable/validate | Enable Sylphon RnD in the launcher, start a new game, inspect the Nym/Rofocale systems and markets, and check bar events |
| Rebuild jar | `powershell -File mods\sylphon\ai\脚本\build.ps1` backs up and installs the jar |
| Offline checks | `ai\脚本\verify\LoadTest.java` uses `-noverify`, matching game vmparams; also `node ai\脚本\tools\check_variants.js <modDir> <gameRoot>` |
| Reproduce/validate localization | See section four of `ai\README_汉化说明.md`; tools in `ai\脚本\汉化\` include loc_csv_quotecheck.js and loc_csv_strict.js |
| Repackage delivery | `powershell -ExecutionPolicy Bypass -File <delivery-skill>\scripts\deliver.ps1 -ModPath mods\sylphon` |
| Version history | In the development Git checkout: `git log` / `git status`, committing each change |

## Notes

- The development mod directory is a local Git repository; `.git` is excluded from delivery archives.
- The important 1.1 fix registered the Meet Eccentric Cieve event creator with `BarEventManager` in `SRD_ModPlugin.onGameLoad()`. Previously, its entire event/intel/mission chain could not appear in any version.
- Delivery archives exclude `jars/src` because deliver.ps1 excludes src folders by default. Use `-IncludeSrc` when a source-inclusive delivery is required.
- Known upstream leftovers, affecting only logs or unused data: 4 SRD_phira_* weapon rows without .wpn files; SRD_goetterdaemmerung_modular.wpn without a CSV row; SRD_Fulgent/SRD_Misja.ship without data rows; and several *OLD or disabled plugin classes. See `ai\报告\最终改动清单.md`.
- The 1.2 release localized data and program text, standardizing the Chinese terminology for Nullspace, Conduit, overload, flux, and AI core.
  The corpus is in `ai\en\` and `ai\zh\`: 460 aligned English/Chinese entries and 593 historical translations without English originals. Glossary: `ai\术语表.json`.
- `data/strings/descriptions.csv` was rebuilt in 1.2 to repair CSV quoting. Subsequent edits must retain valid field quoting:
  Quote fields containing commas, quotes, or line breaks; double embedded quotes; preserve CRLF and UTF-8 without BOM. Then rerun
  `node ai\脚本\汉化\loc_csv_quotecheck.js <file>` and `loc_csv_strict.js <modRoot>`.
