---
layout: page
title: Extensions
permalink: /extensions/
---

Several users in the AtomVM community have provided extensions to AtomVM to support integrations
with specific peripherals and networking protocols.  These extensions are growing and currently
include the following.

## Tools
* [`atomvm_rebar3_plugin`](https://github.com/atomvm/atomvm_rebar3_plugin) A Rebar3 plugin for
streamlining the development of AtomVM applications written in Erlang
* [`ExAtomVM`](https://github.com/atomvm/ExAtomVM) A Mix plugin for streamlining the development of
AtomVM applications written in Elixir
* [`atomvm_packbeam`](https://github.com/atomvm/atomvm_packbeam) Purerlang version of AtomVM
PackBeam utility
* [`uf2tool`](https://github.com/pguyot/uf2tool) Erlang escript to work with .uf2 files

## Drivers

### Nif and port based drivers
* [`atomgl`](https://github.com/atomvm/atomgl) AtomGL is a display driver for a number of different
display models and technologies
* [`atomvm_dht`](https://github.com/atomvm/atomvm_dht) ESP32 DHT11 and DHT12 drivers for AtomVM
* [`atomvm_esp32cam`](https://github.com/atomvm/atomvm_esp32cam) An AtomVM Nif for interfacing with
cameras connected to the ESP32
* [`atomvm_gps`](https://github.com/atomvm/atomvm_gps) Support for GPS device integration
(using ESP-IDF UART/NMEA) on AtomVM
* [`atomvm_led_strip`](https://github.com/harmon25/atomvm_led_strip) ESP32 RMT Nifs for WS2812 and
SK6812 Addressable LEDs (recommended replacement for `atomvm_neopixel` if you need the performance
of a nif based driver)
* [`atomvm_m5`](https://github.com/pguyot/atomvm_m5) A port of
[`M5Unified`](https://github.com/m5stack/M5Unified) for the AtomVM platform
* [`atomvm_mqtt_client`](https://github.com/atomvm/atomvm_mqtt_client) An ESP32 MQTT client library
* [`atomvm_nvs_reset`](https://github.com/atomvm/atomvm_nvs_reset) An IDF component that supports
resetting NVS by holding a pin
* [`atomvm_ssd1306`](https://github.com/atomvm/atomvm_ssd1306) AtomVM ESP32 driver for SSD1306
displays
* [`atomvm_ulp`](https://github.com/pguyot/atomvm_ulp) ESP32 ULP Driver
* [`avm_epdiy_display`](https://github.com/atomvm/avm_epdiy_display) AtomVM EPDiy E-Paper Display
Driver

### BEAM based drivers
* [`atomvm_lib`](https://github.com/atomvm/atomvm_lib) A rich set of libraries for integrating
various sensors and peripherals, including:
    - BME280 Temperature, humidity, and atmospheric pressure sensor
    - BH1750 Luminosity sensors
    - SHT3X Temperature and humidity sensor
* [`atomvm_lora`](https://github.com/atomvm/atomvm_lora) SX127X LoRa modem drivers and libraries
* [`avm_ls`](https://github.com/karlsson/avm_ls) Pure BEAM (Native support for Erlang, Elixir, and
Gleam!) driver for WS2812, SK9822, and AP102 Addressable LEDs (recommended pure BEAM replacement
for `atomvm_neopixel`)

## Libraries
* [`avm_scene`](https://github.com/atomvm/avm_scene) An OTP display orchestration application
* [`mjson`](https://github.com/mbj4668/mjson) Purerlang JSON encoder and decoder designed for
AtomVM

If you have an extension to AtomVM you would like listed here, please [contact](../contact) us.
