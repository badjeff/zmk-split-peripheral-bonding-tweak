# Zmk Split Peripheral Bonding Tweak

This module grant ability to a split central and peripherals to bond on top of a forgettable bluetooth pairing. Two add-on feature are designed to serve this purpose on ZMK.

### feat(Weak Bond)
This feature allow the peripheral re-present its maiden state after reboot. And central shall pair any advertising peripherals on demend. User can build and bond peripheral accessories to existing shields. (e.g. traclball, numpad, touchpad, mouse, and RGB mouse pad)

One downside is surfaced that paring is vulnerable to man-in-the-middle (MITM) attack on pairing. Some PKI key exchange mechanism should be implmented in next release.

### feat(Continuous Scan Window)
This feature allow to set a window of scanning period after bootup for power saving. Otherwise, central will keep scanning if `CONFIG_ZMK_SPLIT_BLE_CENTRAL_PERIPHERALS` counter is not reached.


## What it does

The bluetooth pairing key is cleared on every startup on peripheral, or disconnected to central. The invalid security key should trigger an error after connected and before pairing. While BT stack detected a security change fail, central execute unpair to the peripheral to clear the old security keys. This failsafe routine will force Zephyr redo pairing one more time with new security key.

In multi-peripheral setup, split central is designed to scan, pair and connect all peripherals at once compulsorily. Central does keep scanning BT advertistment until all peripheral is connected. This module patch the central to suspend compulsory scanning after a given time window.

In other words, you can add a trackball shield as a non-compulsory peripheral accessory. Leave it in sleep mode, wake it up, hit `&sys_reset` on central to start repair it. After num pad session is completed and back to sleep mode, hit reset on central to recalibrate peripheral counter. The central is free from compulsory peripherl scanning and become less power hungry.

Speaking of stopping continuous scanning, module [zmk-behavior-insomnia](https://github.com/badjeff/zmk-behavior-insomnia) is also made for same purpose but in different approach. The module is used to keep the primary split peripheral (probably, the right-side shield of a split setup) NOT fall asleep when idle. The outcome keep central does NOT need to scan sleeping peripheral, in meanwhile, it prevent all bluetooth bandwidth being used up in non-compulsory trackball peripheral setup mentioned in above.


## Installation

Include this modulr on your ZMK's west manifest in `config/west.yml`:

```yaml
manifest:
  remotes:
    #...
    # START #####
    - name: badjeff
      url-base: https://github.com/badjeff
    # END #######
    #...
  projects:
    #...
    # START #####
    - name: zmk-split-peripheral-bonding-tweak
      remote: badjeff
      revision: main
    # END #######
    #...
```

And update `shield.conf` on shield.
```
# Enable weak bond
CONFIG_ZMK_SPLIT_BLE_PREF_WEAK_BOND=y
# NOTE: This config is required on the central and all peripheral shields.

# Enable timer to suspend continuous scanning extra peripherals within 30 seconds once any peripheral is connected.
CONFIG_ZMK_SPLIT_BLE_PREF_CONTINUOUS_SCAN_WINDOW=30000
# Then, stop scanning and remember count of connected peripherals of currect session.
# NOTE 1: Auto-reconnect after sleep and wake up still works
# NOTE 2: Use behavior &sys_reset could un-block scanning
# NOTE 3: This only take effect on central
```
