---
title: ESP-12F_Relay_X16
date-published: 2021-09-18
type: relay
standard: global
---

## Product description

This is a 16-relay board with an ESP-12F.

Each relay has COM+NO+NC exposed. Product description rate the relays at 10A, but trust that at your own risk.

The board is powered from an external 24V DC power supply. Apparently, there are 12V and 5V variants.

I bought it from: https://www.aliexpress.com/item/1005003081048304.html (no affiliation, no guarantee it will continue to exist).

Search for more: https://www.aliexpress.com/wholesale?SearchText=esp-12f+16+channel+relay+module

It features:
- 16 SPDT relays with NO/NC/COM exposed via screw terminals
- A screw terminal for DC power
- A reset button and a button you can use for your applications
- One 8-pin header and one 6-pin header (not soldered)
- LEDs: Power indicator, individual relay indicators, plus an additional LED

## GPIO Pinout

The board uses two 74HC595 shift registers as an expander to be able to address all the relays.

These are the important GPIOs to consider in your configuration.

| Pin    | Comment                               |
| ------ | ------------------------------------- |
| GPIOxx | On-board LED                          |
| GPIOxx | On-board button                       |
|        |                                       |
| GPIO14 | 74HC595 Data                          |
| GPIO13 | 74HC595 Clock                         |
| GPIO12 | 74HC595 Latch                         |
| GPIO5  | 74HC595 Output Enable                 |

For completeness, the two exposed headers have the following pins:
- 8-pin header: XXXXXXXXXX
- 6-pin header: XXXXXXXXXX

## Basic Config

```yaml
esphome:
  name: relayboard
  platform: ESP8266
  board: esp12e

# Use the blue LED in the device as a status LED, which will blink if there are warnings (slow) or errors (fast)
status_led:
  pin:
    number: GPIO5
    inverted: True

# Configuration for the 74HC595 expanders
sn74hc595:
  - id: 'sn74hc595_hub'
    data_pin: 14
    clock_pin: 13
    latch_pin: 12
    oe_pin: 5
    sr_count: 2

# Sixteen relays
switch:
  - platform: gpio
    name: "Relay 1"
    pin:
      sn74hc595: sn74hc595_hub
      # Use pin number 0
      number: 0
      inverted: false
# ... add all those you are going to use, up to ...
  - platform: gpio
    name: "Relay 15"
    pin:
      sn74hc595: sn74hc595_hub
      # Use pin number 15
      number: 15
      inverted: false
```
