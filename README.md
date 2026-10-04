# ezeco-zmk

ZMK firmware for the ezeco setup: a Prospector dongle (central) with three wireless peripherals.

| Device | Board | Shield |
|--------|-------|--------|
| Dongle | `seeeduino_xiao_ble` | `ezeco_dongle prospector_adapter` |
| Left half | `cosmos_lemon_wireless_v4` | `ezeco_left` |
| Right half (34mm trackball, PAW3395) | `cosmos_lemon_wireless_v4` | `ezeco_right` |
| Mouse (PAW3395) | `seeeduino_xiao_ble` | `ezeco_mouse` |

Baseline: `rianadon/zmk` `main` (ZMK main of May 2025 on Zephyr 3.5, plus Lemon wireless boards),
`carrefinho/prospector-zmk-module` `main`, `badjeff/zmk-paw3395-driver` `main`.

The keymap (`config/ezeco.keymap`) lives on the dongle and covers all 52 positions:
44 keyboard keys and 8 mouse buttons.

## First pairing

Flash `settings_reset` on every device, then the real firmware. Pair in this order so the
Prospector battery widget shows them left to right: left, right, mouse.

## Provisional parts

- `default_transform` in `boards/shields/ezeco/ezeco.dtsi`: the keyboard RC() positions must be
  replaced with the ones from the Cosmos ZMK export, generated with the Cosmos Program tab
  (peaMK) once the keyboard is wired.
- PAW3395 motion pin on the right half (`ezeco_right.overlay`): VIK AD_2 (P0.31), to confirm
  against the adapter.
- Mouse pins (`ezeco_mouse.overlay`) until the mouse electronics are bench tested.

## Local build

```sh
podman run --rm -v "$PWD/..:/w:Z" -w /w docker.io/zmkfirmware/zmk-build-arm:3.5 bash -c '
  west init -l ezeco-zmk/config && west update && west zephyr-export &&
  west build -s zmk/app -b cosmos_lemon_wireless_v4 -- -DSHIELD=ezeco_left \
    -DZMK_CONFIG=/w/ezeco-zmk/config -DZMK_EXTRA_MODULES=/w/ezeco-zmk'
```
