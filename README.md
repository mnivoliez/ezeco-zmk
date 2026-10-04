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

The keymap (`config/ezeco.keymap`) lives on the dongle and covers 44 positions: 36 keyboard
keys and 8 mouse buttons. It is Miryoku Colemak-DH (default options) written as a plain ZMK
keymap, plus:

- Game / GNum layers, entered with a double tap on the layer-switch row (Nav, Accent, Media:
  left top inner key; Num, Sym, Fun: right top inner key). Leave with GNum + left outer thumb.
- Accent layer (Miryoku's Mouse layer, renamed): é è à ç, € « », Compose; Shift gives capitals.
  Linux mode sends Compose sequences (enable xkb `compose:menu`); Windows mode (toggle `Win` on
  Media) sends Alt codes (NumLock on).
- Ball layer: turns on when the trackball moves. Right hand: N/E/I = left/middle/right click,
  L = precision toggle, U = scroll toggle.
- `U_BOOT` is `&bootloader` (Miryoku default). Soft-off is not used: in ZMK it is global and would
  also switch off the dongle, which has no key to wake it.

## First pairing

Flash `settings_reset` on every device, then the real firmware. Pair in this order so the
Prospector battery widget shows them left to right: left, right, mouse.

## Provisional parts

- `default_transform` in `boards/shields/ezeco/ezeco.dtsi`: the 36 keyboard RC() positions must be
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
