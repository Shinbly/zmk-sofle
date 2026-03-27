# Eyelash Sofle — ZMK Firmware

## Project Overview

Custom wireless split mechanical keyboard (58 keys + 2 rotary encoders) running ZMK firmware on nRF52840 MCUs. Built as a west-based Zephyr module — the repo doubles as a ZMK config repo and a custom board module.

- **Left half** = central (USB + BLE, runs ZMK Studio, holds the keymap)
- **Right half** = peripheral (BLE only, no USB, `col-offset = <7>` in its `.dts`)
- ZMK pinned at **v0.3.0** via `config/west.yml`

## Build Workflow

**No local build.** Firmware is built by GitHub Actions, triggered on push. The workflow reads `build.yaml` which defines three artifacts:

| Artifact | Board | Notes |
|----------|-------|-------|
| Right half | `eyelash_sofle_right` | `nice_view` display |
| Left half (Studio) | `eyelash_sofle_left` | `nice_view` + ZMK Studio over USB |
| Settings reset | `nice_nano_v2` | Clears BLE bonding |

**Flashing**: Download the `.uf2` from Actions → double-tap reset → drag `.uf2` to the USB drive that appears.

## Key Files

| File | Role |
|------|------|
| `config/eyelash_sofle.keymap` | **Active keymap** — edit this |
| `config/eyelash_sofle.conf` | Runtime Kconfig (RGB, sleep, mouse, BLE) |
| `config/west.yml` | West manifest — ZMK version + board module |
| `build.yaml` | CI build targets |
| `boards/arm/eyelash_sofle/` | Hardware board definition (DTS, Kconfig, board metadata) |
| `keymap-drawer/eyelash_sofle.yaml` | Keymap visualization source (keymap-drawer YAML) |
| `keymap_drawer.config.yaml` | Rendering config for keymap-drawer SVGs |

> `boards/arm/eyelash_sofle/eyelash_sofle.keymap` is an **older reference**, not built. Always edit `config/eyelash_sofle.keymap`.

## Keymap Conventions (ZMK DSL)

- **5 layers**: `layer0` (base QWERTY), `layer_1` (nav/mouse/RGB), `layer_2` (BT/bootloader), `layer_3`/`layer_4` (reserved)
- Note the inconsistency: layer 0 is `layer0` (no underscore), layers 1–4 use `layer_N`
- Use `&trans` for pass-through keys on upper layers
- The **active pointing API** is `pointing.h` (`&mmv`, `&msc`, `ZMK_POINTING_DEFAULT_*`). Do **not** use the older `mouse.h` API.
- Encoder on layer 0 → volume; layers 1–4 → scroll via `scroll_encoder` behavior

## Combos & Sleep

- **Soft-off combo**: hold Q + S + Z for 2 s → deep sleep (`hold-time-ms = <2000>`)
- Wake from soft-off: press the physical reset button
- `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=3600000` (1 hour)

## RGB & Backlight

- RGB underglow: off at boot, auto-off on idle and USB, max brightness 60%, hue 160 (blue/purple)
- Backlight (PWM): starts on at 100%, does NOT auto-off on idle
- RGB effect index 3 is used as the starting effect

## Board Architecture

- Matrix: 5 rows × 14 columns; column 6 skipped (encoder position)
- Left: cols 0–5 | Right: cols 7–13 (`col-offset = <7>`)
- GPIO matrix scanner, `col2row` diode direction
- EC11 rotary encoder on P1.10/P1.14
- WS2812 LED strip via SPI3 (P1.12), PWM backlight on P1.13
- `zephyr/module.yml` sets `board_root: .` so west discovers the board

## ZMK Studio

Enabled on the left half only (`snippet: studio-rpc-usb-uart`, `CONFIG_ZMK_STUDIO=y`, `CONFIG_ZMK_STUDIO_LOCKING=n`). Allows live keymap edits over USB without reflashing.

## Keymap Drawer

Visualization uses [keymap-drawer](https://github.com/caksoylar/keymap-drawer). Edit `keymap-drawer/eyelash_sofle.yaml` then regenerate the SVG. Icon syntax: `$$mdi:icon-name$$`. Custom CSS classes (`sym_sub_text`, `tap_dance`, `toggle`, etc.) are defined in `keymap_drawer.config.yaml`.
