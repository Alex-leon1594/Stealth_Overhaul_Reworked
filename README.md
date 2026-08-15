# Stealth_Overhaul_Reworked_v5.12

Complete stealth overhaul for **STALKER Anomaly** (GAMMA-compatible).
Based on the classic addon **«Stealth 2.0.1»** (by xcvb), extended with a full package of new stealth mechanics: noise, detection feedback, silent takedowns and a redesigned detection formula.

## Features

### Core (from Stealth 2.0.1)
- Reworked NPC vision: luminosity, distance, movement velocity, carried weight, outfit/camouflage, crouching and stance all affect how well NPCs see you.
- Custom detection meter (eye icon) and debug HUD.
- Weight-based stealth: the heavier you are, the easier you are to see.
- Camouflage values (`npc_blindness_koeff`) per outfit.
- Full MCM integration with per-option tuning.

### New mechanics (this package)
- **Footstep noise system** (`stealth_noise.script`)
  - Realistic noise radius: crouch **3 m**, walk **10 m**, run **20 m**, sprint **30 m**.
  - Noise scales with total weight, outfit `noise_k` stat, breathing (wounded / low stamina / bleeding), and is reduced by rain.
  - Artifacts alter your noise signature (e.g. `af_gravi` ×1.2, `af_fireball` ×1.3, `af_compass` ×0.75).
  - NPCs investigate noise (threat_danger logic / danger system) — subtle sounds make them curious, loud ones alert them.
  - By default **only hostile factions** investigate your footsteps (MCM `noise_hostile_only`) — neutral NPCs no longer go on alert when you walk or run nearby. Item-drop noise and monster alarms still alert everyone (realistic reactions).
  - Dropping items makes noise (10 m).
- **Squad alarm propagation** (`visual_memory_manager.script`)
  - When an NPC spots you, nearby squad members (within 200 m, MCM `squad_alert_range`) get an alarm boost (×1.6 detection, MCM `squad_alert_dur`).
  - Monsters that see you alarm nearby stalkers (MCM `monster_alarm_radius`, default 150 m; MCM `monster_alarm_dur`). Both radii/durations are configurable live — set to 0 to disable.
- **NPC muzzle flashes** (ported from *NPC's can't see through foliage 1.8.2*)
  - NPCs that fire a non-silenced weapon become highly visible for 0.2 s (×7 detection) — you can spot shooters in the dark.
- **Terrain-dependent camouflage** (ported from *NPC's can't see through foliage 1.8.2*)
  - Ghillie outfits only grant their camo bonus on natural terrain (earth, dirt, grass, bush), tracked via the `actor_on_footstep` callback.
- **Dynamic campfire lighting** (`visual_memory_manager.script`, ported from *NPC's can't see through foliage 1.8.2*)
  - NPCs/monsters standing within 7 m of a burning campfire get +0.4 luminosity — targets near fires are easier to spot (no longer a static constant).
- **Detection feedback HUD** (`stealth_ui.script`)
  - Eye meter: white (undetected) → amber (suspicion / alarm) → red (spotted).
  - Noise bar to the right of the eye icon shows your current noise radius as a vertical meter (green = quiet → yellow → red = very loud). Can be hidden independently via MCM `noise_bar` while keeping the noise system active — or toggled in-game with a hotkey (MCM `noise_bar_key`, default F7).
  - Eyesight is not persistent: your detection accumulates, then calms down along a smooth exponential curve (fast drop right after losing sight, gentle tail) — roughly halved every 2.5 s.
  - Companions are excluded from the meter: any NPC traveling in your squad (escorts/companions) never turns the eye red or amber, even when their goodwill is not FRIENDS.
- **Dual HUD icon** (`light_gem.script`, ported from *Stealth 2.31*)
  - Two modes selectable in MCM (`icon_type`): "Light indicator" (classic light gem) or "NPC vision" (progress bar — white normal, yellow combat/danger, red spotted).
  - Icon position is fully configurable in MCM (`icon_x` / `icon_y`).
- **NPC NVG system** (`stealth_nvg.script`, ported from *Stealth 2.31*)
  - Higher-rank stalkers may own NVGs (experienced/professional 20%, veteran/expert 50%, master 85%, legend 100%) — at night they show a **visible red dot** at the eye and **turn off their headlamp**.
  - NPCs that own NVGs see better at night (`nvg_val` multiplier); toggle with `npc_nvg`, disable entirely with `nvg_val = 0`.
- **Night vision & light interactions**
  - NPC night adaptation: in dark zones, NPCs coming from bright areas take a moment to adjust (×0.6 detection).
  - Your own flashlight pointed at an NPC's back massively blinds them (×0.55, marks them as suspicious).
  - Guards/snipers on watch are **less** perceptive (×0.5); searching NPCs are **more** perceptive (×1.5).
- **Scent system** — mutated animals track you by blood: the more you bleed, the farther they smell you (up to ~60 m).
- **Community perception** — factions perceive you differently (Duty 1.2, Military 1.25, Renegades 0.9, Ecologists 0.85…).
- **Silent takedowns** (`stealth_takedown.script`)
  - Crouch, knife equipped, sneak behind an NPC who hasn't noticed you → the next knife swing is a silent kill.
  - Kills fire the same callbacks as *Stealth Kill Detection Fix* (`npc_on_death_callback` with the weapon as killer so NPCs don't aggro, plus `npc_on_silent_kill_callback` and the `silent_kills` statistic) — quests, loot and stats see your silent kills. Safe `pcall` guards make this a no-op if the fix isn't installed.
  - Hide corpses: crouch and look at a dead body for 1.5 s to remove it from the world (no alarm, no detection). Hidden corpses survive save/load — they are re-hidden and keep suppressing danger reactions after a reload.
- **Danger system refinement** (`xr_danger.script`)
  - Dangers caused by the player persist longer and are never ignored by distance.
  - Hidden corpses no longer trigger danger events.

## Requirements

- STALKER Anomaly 1.5.1+ (works with GAMMA)
- **[MCM](https://www.moddb.com/mods/stalker-anomaly/addons/mod-configuration-menu)** (optional but recommended — without it the defaults in `stealth_mcm.script` are used)

## Installation

1. Copy the `gamedata` folder into your Anomaly root directory (overwrite when asked).
2. If you use GAMMA/MO2: install the archive as a normal mod or place `gamedata` manually.
3. Launch the game — the mod is configured by default with all new mechanics **enabled**.

> The included files **override** parts of the base stealth files. If you use another stealth mod, install only one of them.

## Configuration (MCM)

`Options → Mod Configuration Menu → Stealth`

| Option | Default | Description |
|---|---|---|
| `memory` | 1.0 | How fast NPCs forget you after losing sight |
| `luminocity` | 1.0 | Overall effect of light/darkness on detection |
| `distance` | 1.0 | Effect of distance on detection |
| `velocity` | 1.0 | Effect of movement speed on detection |
| `weight` | 1.0 | Effect of carried weight on detection |
| `crouch` | 0.4 | Detection multiplier while crouched |
| `low_crouch` | 0.25 | Detection multiplier while crouched + slow movement |
| `icon` | ON | Show the detection eye / noise bar HUD |
| `icon_type` | 1 | HUD icon mode: 1 = light indicator (gem), 2 = NPC vision (progress bar) |
| `icon_x` | 970 | Icon X position (0–1000) |
| `icon_y` | 650 | Icon Y position (0–1000) |
| `nvg_val` | 1.0 | Night-vision multiplier for NPCs that own NVGs (0 disables the NVG system, 0–3) |
| `npc_flash` | OFF | NPCs that see another NPC's active flashlight from afar get a brief vision boost. Adds immersion; off by default due to potential FPS impact on dense maps |
| `michiko_patch` | OFF | Alternative (harsher) night-time luminosity curve |
| `debugx` | OFF | Debug printing for the target NPC under your crosshair |
| `logging` | ON | Write the dedicated log file (`logs/stealth_overhaul.log`) |
| `noise` | ON | Enable the footstep noise system |
| `noise_bar` | ON | Show the blue noise-level bar under the HUD icon (hide it without disabling the noise system) |
| `noise_bar_key` | F7 | Hotkey to toggle the noise bar in-game (0 = unbound, set in MCM) |
| `noise_scale` | 1.0 | Global noise radius multiplier (0.5–3) |
| `noise_handoff` | ON | Delegate hostile footstep alerts to AlifeTactics when installed (no double alerting) |
| `noise_hostile_only` | ON | Only NPCs whose faction is hostile to you react to your footsteps (neutrals/friends ignore them — no more alert spam when walking near neutrals) |
| `scent` | ON | Mutants smell your blood |
| `glare` | ON | Flashlight blindness / backlight glare |
| `adaptation` | ON | NPC eyes adapt between light and dark zones |
| `community` | ON | Faction-specific perception |
| `npc_nvg` | ON | NPCs with active NVGs see better at night |
| `takedown` | ON | Enable silent knife takedowns |
| `hide_corpse` | ON | Enable corpse hiding (crouch + stare 1.5 s) |
| `monster_alarm_radius` | 150 m | Radius: monsters that spot you alarm nearby stalkers (0 = disabled, 0–300) |
| `monster_alarm_dur` | 4 s | Duration of the monster alarm boost (0–10 s) |
| `squad_alert_range` | 200 m | Radius: squadmates of a spotting NPC get an alert boost (0 = disabled, 0–500) |
| `squad_alert_dur` | 3 s | Duration of the squad alert boost (0–10 s) |
| `at_disclosure_gate` | ON | When AlifeTactics is installed: squadmates only learn your position after an attack if they see you or are already fighting you (no "magic" squad-wide reveal) |

## Files

```
gamedata/scripts/
├── stealth_log.script           Dedicated log file (logs/stealth_overhaul.log)
├── stealth_noise.script          Noise system, NPC registry, suspicion, squad/monster alerts
├── stealth_ui.script             Detection eye, monster alert
├── stealth_takedown.script       Silent kills, corpse hiding
├── stealth_nvg.script            NPC NVG system (rank-gated, eye dot particle, headlamp off)
├── light_gem.script              Dual HUD (light gem / NPC vision bar, movable icon)
├── visual_memory_manager.script  Core detection formula (overrides Stealth 2.0.1), campfire lighting
├── trans_outfit.script           Empty stub for `timer_trans` (bind_stalker compatibility)
├── xr_danger.script              Danger system, actor-related dangers
├── xr_combat_ignore.script       Safe-zone fix
└── stealth_mcm.script            MCM options

gamedata/configs/
├── ai_tweaks/xr_danger.ltx       Danger inertion/ignore values (actor sections added)
├── creatures/m_stalker.ltx       Footstep/vision tuning for stalkers
├── creatures/m_stalker_zombied.ltx  Zombied vision tuning (vision_free / vision_danger)
├── ui/ui_light_gem.xml           HUD statics (gem, gem_bar, det_eye, noise_bar)
├── ui/ui_light_gem_16.xml        HUD statics (16:9)
├── ui/ui_light_gem_21.xml        HUD statics (21:9)
└── text/eng+rus+spa/ui_st_stealth.xml  Localized strings

gamedata/particles/stealth_nvg/
├── nvg_dot_green.pe   NVG eye dot — allies (green)
├── nvg_dot_yellow.pe  NVG eye dot — neutrals (yellow)
└── nvg_dot_red.pe     NVG eye dot — enemies (red)
gamedata/textures/ui/lightgem/eye.dds           Detection eye texture
gamedata/textures/ui/lightgem/eye2.dds          NPC vision bar texture
gamedata/textures/ui/lightgem/circle.dds        Noise/vision circle texture
gamedata/textures/ui/lightgem/bar_noise.dds     Noise bar track (empty state, blue->green gradient)
gamedata/textures/ui/lightgem/bar_noise_fill.dds White fill for the noise bar (tinted green/yellow/red)
```

## Compatibility

Fully compatible with the GAMMA Alife pack — no shared files, no callback conflicts:

- **xlibs 1.8.4** — no interaction (library only).
- **AlifePlus 1.8.6** — no interaction (NPC lifecycle management; this mod's ID-based registry tolerates its spawn/despawn churn).
- **AlifeBalance 1.1.3** — no interaction (smart/squad balance only).
- **AlifeGuard 1.3.1** — no interaction (NPC guarding/sanitizer).
- **AlifeTactics 1.1.6** — designed-in coexistence:
  - AT patches the winning `xr_danger` at runtime (function-level, on_game_start). This mod's `xr_danger.script`/`xr_danger.ltx` are the VFS winners and expose everything AT probes (`actid`, `DangerIgnoreActor`, the seven patched entry points), so AT's danger scheme runs on this mod's config values.
  - **Hidden corpses** never trigger danger, even under AT: the suppression now lives in a global `npc_on_eval_danger` subscriber (`stealth_takedown.script`) that fires under vanilla, this mod's, or AT's danger evaluator.
  - **Footstep noise**: when AT's movement-noise is enabled, hostile stalkers' footstep alerts are delegated to AT (no double `set_script_danger` stamping); this mod keeps its HUD noise bar, item-drop noise and monster alarms, and with the MCM `noise_hostile_only` option (default ON) only factions hostile to the actor react to footsteps. Toggle with the MCM `noise_handoff` option.
  - **Hit-disclosure gate** (`stealth_noise.script`): AT's `at_disclosure` force-injects the shooter into the whole squad's memory on the first faction-enemy hit — even when nobody saw or heard the attack. This mod wraps `xcombat.disclose_enemy` (AT's only disclosure entry point, resolved live at call time) so that actor-initiated disclosures only go through when the squad member can actually see the actor or is already fighting him. NPC-vs-NPC disclosures and all non-actor callers pass through untouched; the return value is only used for counting in AT, so a suppressed disclosure breaks nothing. Toggle with the MCM `at_disclosure_gate` option (default ON).
  - AT's own `at_noise` handles reload/item-use sounds; this mod handles item drops — complementary.

> **Note:** mods that ship their own `visual_memory_manager.script` (e.g. *NPC's can't see through foliage 1.8.2 DLTX*, GAMMA's Atmospherics/Stealth Crash Fix) are mutually exclusive with this mod's copy — give this mod higher priority in MO2. The foliage vision itself works via DLTX configs and is unaffected.

## Balance tuning tips

- **Too hard to sneak?** Lower `noise_scale`, disable `npc_nvg` / `adaptation` / `community`, or raise `crouch` values in MCM.
- **Too easy?** Set `noise_scale` to 1.5–2 and lower `crouch` to 0.3/0.15.
- Outfit noise stats are read from the `noise_k` line in the outfit section (add it to any outfit to make it quieter/louder).

## Changelog

### 2026-08-15 — v5.12: Code Quality & Bug Fixes

Internal quality pass — no gameplay changes, no save compatibility breaks.

- **Bug fix — `stealth_noise.script`:** `compute_radius()` was defined **twice** in the same file; Lua silently used the second (uncached) definition, leaving the optimised cached version (outfit / artifact slots) completely dead. Removed the duplicate. The active version now correctly caches the outfit multiplier and belt-artifact multipliers, avoiding an `iterate_inventory` call on every 250 ms update.
- **Performance — `stealth_noise.script`:** `purge_stale()` was calling `level.object_by_id()` in a loop on every 250 ms tick. The engine-driven `on_net_destroy` callback already removes objects immediately; `purge_stale` is now throttled to run at most **once per second** as a safety fallback — reducing hot-path cost with no correctness impact.
- **API — `stealth_noise.script`:** Added public `remove_from_registry(id)` function so other modules (`stealth_ui`) no longer reach into the `online_npcs` table directly.
- **Cleanup — `stealth_ui.script`:** Now calls `stealth_noise.remove_from_registry()` instead of writing to `online_npcs` externally.
- **Bug fix — `stealth_mcm.script`:** Four separator items all shared the same `id = "divider"` in the MCM tree. They are now uniquely named `divider_a / b / c / d`.
- **New MCM option — `npc_flash`** (default OFF): NPCs that observe another NPC with an active flashlight get a brief vision boost. Previously this was hardcoded to `false` with a comment saying "change this if FPS tanks". It is now a proper MCM checkbox, exposed in all three localisations (EN / RU / ES). Default remains OFF to avoid FPS impact on dense maps.
- **Cleanup — `stealth_takedown.script`:** Corpse-registry pruning replaced the unreliable `tg % 30000 < 200` check (which fired only if the update landed within a 200 ms window of the 30 s mark) with a proper `corpse_cleanup_timer` variable — cleanup now runs deterministically every 30 seconds.
- **Cleanup — `stealth_nvg.script`:** Translated all Spanish-language code comments to English for consistency.
- **Cleanup — `xr_danger.script`:** Removed three permanently-disabled `--[[ ... --]]` blocks (warning-shot behaviour and a smart-terrain alarm call that were commented out).

### 2026-08-14 — v5.11: Bug Fixes, Balance & New Features
- **Crash Fix (`stealth_nvg.script`):** Fixed a fatal error (`FATAL ERROR... item not found, id`) that occurred when an NPC without a valid character profile spawned.
- **Balance - NPC Vision & Accuracy (`m_stalker.ltx`):** Tuned vision parameters (FOV, detection time, etc.) and weapon dispersion to create more fair and tactical combat.
- **Balance - Immunity Progression (`m_stalker.ltx`, `m_stalker_zombied.ltx`):** Reworked immunities for stalker ranks and zombified for a clearer difficulty curve and fixed an invulnerability bug.
- **Optimization - Noise Calculation (`stealth_noise.script`):** Optimized the footstep noise calculation by caching outfit and artifact multipliers, reducing CPU load.
- **New Feature - Color-Coded NVG (`stealth_nvg.script`):** The NVG light dot on NPCs' eyes now changes color based on their relation to the player (Green: Ally, Yellow: Neutral, Red: Enemy), providing immediate visual feedback.

### 2026-08-12 — v5.10: script optimization
- **Removed redundant script** (`camp_lum.script`): its campfire lighting detection is duplicated — and better implemented — in `visual_memory_manager.script` (throttled to 1 Hz). The `luminocity_inc` variable it defined was not used by any other script.
- **Logging optimization** (`stealth_log.script`): the `log` function now caches the log folder path (`log_path`) after locating it the first time — no more repeated filesystem probes on every write.
- **HUD optimization** (`light_gem.script`, `stealth_ui.script`): MCM settings are now cached in a local `cfg` table, read once at game start and refreshed only when an MCM option changes — values are no longer fetched every frame in the UI `Update` loops.
- **Detection formula optimization** (`visual_memory_manager.script`): the hot-path `get_visible_value` function now uses the cached `cfg.debugx` value instead of calling `stealth_mcm.get_config("debugx")` on every evaluation.

### 2026-08-11 — v5.9: neutral NPCs no longer react to footsteps
- **Neutral NPCs ignore your footsteps** (`stealth_noise.script`, `stealth_mcm.script`): players reported that neutral NPCs kept going on alert (`threat_danger`) whenever the player walked, ran or sprinted near them. Root cause: the noise system only excluded FRIENDS (goodwill >= 1000) from investigating, so neutrals (goodwill 0) were treated like hostiles within the footstep radius (walk 10 m / run 20 m / sprint 30 m) every 250 ms.
- **New MCM option `noise_hostile_only`** (default ON) + localized strings (EN/RU/ES): only stalkers whose engine relation to the actor is ENEMY (`relation == game_object.enemy`, matching the `game_relations.ltx` attitude thresholds) now investigate footstep noise. Neutrals and friends no longer go on alert when you walk or run near them. Disable the option to restore the old behaviour (everyone reacts to footsteps).
- Item-drop noise (`noise_at`) and monster alarms (`alarm_nearby`) are unchanged — they still alert neutrals, which keeps realistic reactions to loud events.

### 2026-08-09 — v5.8: noise-bar hotkey, hidden corpses survive saves, cheaper HUD
- **Noise bar hotkey** (`stealth_mcm.script`, `light_gem.script`): new MCM option `noise_bar_key` (default F7, 0 = unbound). Pressing the key toggles the noise bar in-game without opening the menu. Registered via the `on_key_press` callback with menu/dialog guards (blocked in menus, trade and conversations — guide pattern), localized ON/OFF tips (EN/RU/ES), and the override resets whenever any MCM option changes.
- **Hidden corpses persist across saves** (`stealth_takedown.script`): the hidden-corpse registry is saved/loaded through the `save_state`/`load_state` callbacks. After a reload, corpse objects that still exist are re-hidden (`set_visible(false)` + `set_corpse_ready(false)`, both `pcall`-guarded) and keep suppressing NPC danger reactions — no more "reappearing" hidden corpses after loading.
- **Performance** (`stealth_ui.script`): the expensive line-of-sight checks (`see()`) in the detection-eye and monster passes now run at most every 200 ms (down from every 100 ms tick), while the meter/decay math still updates at 10 Hz. Halves the `see()` load (~400 → ~200 calls/s); the red/amber state refreshes at 5 Hz, imperceptible visually.
- **Smarter drop noise** (`stealth_noise.script`): dropping an item now alerts NPCs within `max(10 m, current noise radius)` instead of a fixed 10 m — a dropped can while sprinting is heard farther.
- **Crash fix** (`xr_danger.script`): `danger:time()` can return `false`, which made `eval_danger` do arithmetic on a boolean → fatal Lua error (`lua_pcall_failed`) on modded exes. `get_danger_time` and the line-318 check already coerce via `tonumber(...) or 0`; now the stored evaluator state (`self.a.danger_time`) and the public `set_script_danger` input are coerced the same way, so even a save created by a pre-fix build or a third-party mod calling `set_script_danger` with `false` can no longer trigger the crash.

### 2026-08-09 — v5.7: companions excluded from the detection meter + independent noise-bar toggle
- **Companions never light the detection eye** (`stealth_ui.script`): `is_ally_of_actor` now also treats any NPC traveling in the actor's squad (escorts/companions) as an ally — they no longer turn the eye red/amber or contribute to the meter, even when their goodwill to the actor is below FRIENDS. Squad detection is checked via `get_squad()` with `pcall` guards (no squad → falls back to the goodwill check).
- **New MCM option `noise_bar`** (default ON) + localized strings (EN/RU/ES): the blue noise-level bar under the HUD icon can now be hidden independently (`light_gem.script`) while the footstep noise system keeps working.

### 2026-08-08 — v5.6: no magic squad knowledge (AlifeTactics hit-disclosure gate)
- **New MCM option `at_disclosure_gate`** (default ON) + localized strings (EN/RU/ES). When AlifeTactics is installed, AT's `at_disclosure` force-injects the shooter into the whole squad's memory on the first faction-enemy hit, even when nobody saw or heard the attack. This mod wraps `xcombat.disclose_enemy` (AT's only disclosure call site, resolved live at call time — covers hit, redisclose and inherit paths) so actor-initiated disclosures only reach squad members who can actually see the actor or are already fighting him. NPC-vs-NPC disclosures and all non-actor callers pass through unchanged; the return value is only used for counting in AT, so suppression is side-effect free. Verified: no other mod in the GAMMA pack calls `disclose_enemy`, `pcall` guards on `see()`/`best_enemy()` prevent dead-object crashes.

### 2026-08-08 — v5.5: configurable alarm radii
- **New MCM options** (`stealth_mcm.script`): `monster_alarm_radius` (0–300 m, default 150), `monster_alarm_dur` (0–10 s, default 4 s), `squad_alert_range` (0–500 m, default 200), `squad_alert_dur` (0–10 s, default 3 s). The monster alarm (`stealth_ui.script`) and the squad alert (`visual_memory_manager.script`) now read them live every tick — no restart needed; setting a radius to 0 disables the respective system entirely.

### 2026-08-08 — v5.4: full compatibility with the GAMMA Alife pack
- **Hidden-corpse danger suppression is now universal** (`stealth_takedown.script`): moved into a global `npc_on_eval_danger` subscriber. It fires no matter which `xr_danger` wins the VFS — vanilla, this mod's copy, or the AlifeTactics runtime patch — so a hidden corpse never makes NPCs react. The internal check in `xr_danger` stays as reinforcement.
- **Footstep noise cooperates with AlifeTactics** (`stealth_noise.script`): auto-detects AT at runtime (`at_mcm` + `danger_move_noise`). When AT's movement-noise is active, hostile stalkers inside this mod's noise radius are skipped (AT stamps them with its own radii) — no double `set_script_danger`. Item drops, monster alarms, HUD noise bar and neutral-NPC investigation remain this mod's own. New MCM option `noise_handoff` (default ON) forces this mod's full system when disabled.
- **New MCM option `noise_handoff`** + localized strings (EN/RU/ES).
- Verified against xlibs 1.8.4, AlifePlus 1.8.6, AlifeBalance 1.1.3, AlifeGuard 1.3.1, AlifeTactics 1.1.6: no shared files, no callback collisions, AT probes (`actid`, `DangerIgnoreActor`, entry points) satisfied by this mod's `xr_danger`.

### 2026-08-08 — v5.3: stability fixes (verified on MT-TEST build 10039)
- **Fatal dead-NPC crash fixed** (`visual_memory_manager.script`): `npc_on_before_hit` now guards `npc:alive()` and only reads the pending-hit record when it exists, before calling `npc:see(db.actor)` — an NPC that dies and hits the actor on the same tick (e.g. a bloodsucker's death attack) no longer crashes the engine with `cannot check visibility of dead object` / `GetAttitude`.
- **Dedicated log made resilient** (`stealth_log.script`): the log path is now resolved through three candidates (`$logs$` virtual path → `appdata/logs` → `fs_root/appdata/logs`) with `get_log_path()` as a last resort; a `nil` config value counts as **enabled** (only `false` disables); if the path or `io.open` fails, a one-time `!StealthOverhaul:` diagnostic is printed into `xray_*.log` instead of failing silently.
- **MCM robustness** (`stealth_mcm.script`): `get_config` falls back to the built-in defaults when `ui_mcm.get` returns `nil` (option not registered / bad path) — no more nil-index errors on startup.
- Verified live: all stealth scripts load with zero errors and `logs/stealth_overhaul.log` is created and written.

### 2026-08-07 — v5.2: allies no longer detect the player
- **Allied NPCs are excluded from the stealth system** (`stealth_ui.script`, `visual_memory_manager.script`, `stealth_noise.script`): a stalker whose goodwill to the actor is `>= game_relations.FRIENDS` no longer contributes detection to the eye icon, never accumulates `vis_acc` in the detection formula, and won't investigate the player's noise or get alarmed by monsters spotting him. Only enemies, neutrals and hostile mutants still detect you.
- Detection icon now only reacts to non-allied NPCs; allied squads nearby no longer trigger amber/red states.

### 2026-08-07 — v5.1: fix — NPCs in alert when standing still
- **Fixed** (`stealth_noise.script`): when the player was standing still, `compute_radius()` fell through to the `run` branch (20 m), so NPCs within 20 m of the player were put into alert (`threat_danger`) every 250 ms — most visible right after loading a save near friendly NPCs. The radius is now `0` when the player isn't moving (`mcAnyMove`), and movement states are mapped in the correct order (sprint 30 m, crouch-walk 3 m, walk 10 m, run 20 m).

### 2026-08-07 — v5: localization & HUD defaults
- **Russian translation rewritten** (`text/rus/ui_st_stealth.xml`): fixed garbled machine-translated strings (mojibake), full retranslation of every option and description.
- **Spanish translation cleaned** (`text/spa/ui_st_stealth.xml`): accents removed from all strings to avoid encoding issues in-game.
- **Default HUD icon position changed** from `700/700` to `970/650` (`stealth_mcm.script` defaults + MCM sliders), and the light-gem statics in `ui_light_gem.xml` / `ui_light_gem_16.xml` / `ui_light_gem_21.xml` were repositioned and resized (24×32) to match the 21:9/16:9/16:10 layouts.
- Validated in-game: full session (Cordon, quicksave) with zero errors from all stealth scripts.

### 2026-08-06 — v4: engine-native improvements
- **Event-driven NPC registry** (`stealth_noise.script`): `online_npcs` is now maintained via the engine's `game_object_on_net_spawn` / `game_object_on_net_destroy` callbacks (NPCs/monsters added/removed instantly, online/offline switching included) instead of waiting for the periodic 5 s `db.storage` scan — the periodic refresh remains as a safety fallback.
- **Noise-radius debug visualization** (`stealth_noise.script`): with the MCM `debugx` flag ON, the current noise radius is drawn in-world via the Modded Exes `debug_render` API (translucent blue sphere around the actor).
- **Fixed `xr_combat_ignore.script`**: the stalker-vs-military Cordon distance rule compared `comm` twice instead of `ene_comm` (`comm == "stalker" and comm == "army"` → `ene_comm == "army"`).

### 2026-08-06 — v3: takedown crash fix
- Fixed a fatal error in `stealth_takedown.script` (`attempt to index local 'obj' (a boolean value)`) that crashed the game on `actor_on_update`: the takedown/hide-corpse loops iterated the NPC registry values as objects, but the registry stores `true` markers (`id → true`). Both loops now resolve the NPC via `level.object_by_id(id)` like `stealth_noise`/`stealth_ui`, and `alive()` returns `false` for non-object values.
- The ID-based registry rule now holds across all scripts: NPCs are tracked by ID only and resolved on demand — destroyed objects can never crash a callback.

### 2026-08-06 — v2.31 features ported
- **NPC NVG system** ported from Stealth 2.31: rank-gated NVG ownership, visible eye-dot particle at night, NVG owners switch off their headlamp (`sr_light` patch). Works on Anomaly 1.5.2 and 1.5.3.
- **Dual HUD** ported from Stealth 2.31: `icon_type` (light indicator / NPC vision bar) + movable icon (`icon_x`/`icon_y`) — replaces the fixed light gem (`light_gem_mcm.script` removed).
- The night-vision detection boost in `visual_memory_manager.script` is now gated by actual NVG ownership (`npc_nvg` + `stealth_nvg.has_nvg`) instead of applying to every NPC.

### 2026-08-06 — Stability rewrite (v2)
- Rewrote `stealth_noise.script`, `stealth_ui.script` and `stealth_takedown.script` around an **ID-based registry**: NPCs are tracked by ID only and resolved on demand via `level.object_by_id()` — destroyed objects can never crash a callback.
- Only real engine callbacks are registered (`actor_on_update`, `actor_on_item_drop`); nonexistent ones (`npc_on_binder_online/offline`, `actor_on_bolt_throw`) were removed.
- Fixed a fatal `level` shadowing bug in `noise_at()` (parameter hid the global level table) that crashed on item drop / disconnect.
- Added `alive()` guards to the detection-eye loop so dead-but-not-destroyed NPCs are skipped (fixes "cannot check visibility of dead object" spam).
- Bolt-throw noise removed — the `actor_on_bolt_throw` callback does not exist in Anomaly; item-drop noise (10 m) remains.

## Credits

- **xcvb** — original Stealth 2.0.1 addon
- **Alundaio** — NPC muzzle flash, terrain camo and campfire lighting ported from *NPC's can't see through foliage 1.8.2 DLTX*
- Addon page: <https://www.moddb.com/mods/stalker-anomaly/addons/stealth1>

> **Note:** if you use *NPC's can't see through foliage 1.8.2 DLTX* alongside this mod, disable its `visual_memory_manager.script` (or give this mod higher priority in MO2) — both files are mutually exclusive. The foliage vision itself works via DLTX configs and is unaffected.
