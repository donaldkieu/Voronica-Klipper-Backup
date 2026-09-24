# Voronica — Claude Code Project Instructions

Voron 2.4 (300mm CoreXY) running Kalico firmware, on this Pi (CM4) at ~/printer_data/config.
This directory is the working copy of `donaldkieu/Voronica-Klipper-Backup`. You are working on
LIVE printer configuration for a physical machine — treat every edit as something that runs on
real hardware, not a sandbox.

## Before answering anything about config, commands, or hardware

1. **Read the actual file, not memory.** If the question involves printer.cfg, macros.cfg,
   steppers.cfg, or any .cfg file — read the current file in this repo before answering. Don't
   rely on a prior read from earlier in the session if the file could have changed since (run
   `git status` / `git pull` first if unsure).
2. **Check docs/ before stating any config key, command, or plugin parameter exists.** See
   "Reference docs" below. If it's not in docs/ or official upstream docs, say so explicitly —
   never invent a config key, macro, pin name, or command parameter.
3. **Full reads only.** No grep-and-answer on config or log files — read the whole relevant
   section, or the whole klippy.log session, before concluding anything. Partial reads have
   caused real misdiagnoses on this machine before (a fan-stoppage/MCU-dropout root cause was
   missed this way).
4. **Source wins over reasoning.** If a file you just read or a doc you just fetched contradicts
   general Klipper knowledge, the source is right. This machine runs Kalico, not vanilla
   Klipper — don't suggest vanilla-Klipper workarounds unless explicitly asked to test
   cross-compatibility.
5. **Generating a .cfg block or full file:** confirm you've read the current live version of
   that exact file, and confirmed every hardware detail against it, before writing a single line.
6. **New hardware mentioned that isn't below:** flag it, ask whether to add it here, and research
   it (official docs + GitHub + community) before answering questions about it.

## Ask before proceeding

- Hardware wiring or pin changes
- Firmware flashing
- Changes to homing, QGL, or Z calibration sequences
- Anything that could crash the printer or damage hardware mid-print

Confirm printer state (idle / printing / paused) before touching anything live.

## Reference docs

Curated references belong in `docs/` in this repo — copy the whole set in from the Claude
Project (not just the 7 named in the instructions doc's lookup table — the Project has 24,
including two profile files the checklist requires checking but that table omits):

- **Profile (check these before asking the user anything):** `VORONICA_TUNING_STATE.md`,
  `VORONICA_HARDWARE.md`, `VORONICA_ASSISTANT_INSTRUCTIONS.md`, `VORONICA_CONFIG_REFERENCE.md`
- **Klipper/Kalico core:** `DOC_KLIPPER_KALICO.md`, `DOC_KLIPPER_GCODES.md`,
  `DOC_KLIPPER_STATUS_REFERENCE.md`, `DOC_KLIPPER_COMMAND_TEMPLATES.md`,
  `DOC_KLIPPER_CONFIG_CHECKS.md`, `DOC_KLIPPER_FAQ.md`
- **Motion & tuning:** `DOC_SHAKETUNE_MOTORSSYNC.md`, `DOC_KLIPPER_MEASURING_RESONANCES.md`,
  `DOC_KLIPPER_RESONANCE_COMPENSATION.md`, `DOC_KLIPPER_ROTATION_DISTANCE.md`,
  `DOC_KLIPPER_PRESSURE_ADVANCE.md`, `DOC_KLIPPER_TMC_DRIVERS.md`,
  `DOC_KLIPPER_AXIS_TWIST_SKEW_EXCLUDE_MULTIMCU.md`
- **Bed/probe:** `DOC_BEACON3D.md`, `DOC_KLIPPER_BED_MESH.md`,
  `DOC_KLIPPER_BED_LEVEL_AND_PROBE_CALIBRATE.md`, `DOC_KLIPPER_MANUAL_LEVEL.md`
- **Other:** `DOC_KLIPPER_CANBUS.md`, `DOC_KLIPPER_HARDWARE_SENSORS.md`,
  `DOC_KLIPPER_SLICERS_AND_CONFIG_CHANGES.md`

Lookup order: profile files first (Voronica-specific values, known mistakes, current tuned
state) → the relevant topical DOC_*.md file (exact syntax/parameters) → official upstream docs
(kalico.gg, klipper3d.org, docs.beacon3d.com, plugin GitHub repos) for anything not covered —
fetch these, don't answer from training data on config keys or plugin behaviour → community
sources (r/voroncorexy, r/klippers, GitHub issues) for real-world edge cases official docs are
silent on. Label community findings `[community]` with source, date, and corroboration status.

## Known gotchas — add to this list whenever a new one is confirmed

- `can0-up.service` is disabled on purpose — do not re-enable, it conflicts with systemd-networkd.
- LED frame rate must stay at 8fps — 12fps caused EBBCan overload → `Timer too close` crash during
  Beacon contact homing.
- klipper-led_effect is currently uninstalled (as of 2026-08-26) — don't assume LED effects are
  active.
- Beacon plugin is on v2.0.0-31, which has the `non_critical_recon_event` crash bug; v2.0.0-34
  fixes it but hasn't been applied yet.
- Dragon Dinghy's CAN bridge capability is unverified in Klipper — its STM32F072 appears to lack
  the CAN peripherals Klipper requires. Don't recommend relying on it without confirming with
  Dragonkitty first.
- mainsail-AFC has no published install procedure — point to the Armored Turtle Discord, don't
  guess at steps.
- BoxTurtle needs a 24V PSU rated above 3.2A (4× TMC2209 @ 0.8A = 3.2A minimum) — the UC-LGY-24V
  (3A) is insufficient.

## Format

Config changes: `.cfg` deltas only, preserve existing comments and disabled lines, use code
blocks. Tuning results: tables. Keep answers concise and direct — no filler, no restating the
question.

## After something you suggested doesn't work

1. State plainly what failed and why — don't guess.
2. Re-read the actual source (file or doc) before proposing anything else.
3. Quote the specific line that shows the correct approach.
4. Only then give the corrected answer.
