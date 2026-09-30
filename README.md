# zmk-dongle

A generic, keyboard-agnostic dongle shield for [ZMK](https://zmk.dev).

With a [dongle](https://zmk.dev/docs/hardware-integration/dongle), a spare
BLE controller becomes the *central* part of your split keyboard, and every
half becomes a low-power *peripheral*.

Usually, each keyboard needs its own dongle shield, with a copy of its matrix
transform and physical layout. This module does not: the `dongle` shield is
stacked on the keyboard's own central-side shield, which already provides
both, and it only replaces the key matrix with a keyless one.

## Usage

Add this module to your `config/west.yml`, then build the dongle by adding
`dongle` after your keyboard's left shield:

```yaml
include:
  # dongle: any BLE board, no keys
  - board: nice_nano@2//zmk
    shield: corne_left dongle
  # left half, turned into a peripheral
  - board: nice_nano@2//zmk
    shield: corne_left
    cmake-args: -DCONFIG_ZMK_SPLIT=y -DCONFIG_ZMK_SPLIT_ROLE_CENTRAL=n
  # right half: already a peripheral, unchanged
  - board: nice_nano@2//zmk
    shield: corne_right
```

The keymap is picked up as usual (`corne.keymap`), since the dongle build
still uses the keyboard's shield name.

Flash `settings_reset` on every part before switching to or from dongle mode.

## Scope

- Works with keyboards defined as *shields* whose key matrix is labelled
  `kscan0` (the ZMK convention); the build fails explicitly otherwise.
- Keyboards defined as *boards* (Glove80, Go60, Corneish Zen…) have no
  shield to stack on and still need a dedicated dongle shield.
- Assumes two halves. For a unibody keyboard, add
  `-DCONFIG_ZMK_SPLIT_BLE_CENTRAL_PERIPHERALS=1 -DCONFIG_BT_MAX_CONN=6 -DCONFIG_BT_MAX_PAIRED=6`.
