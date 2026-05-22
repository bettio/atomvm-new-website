---
layout: post
title: "Announcing AtomVM v0.7.0-alpha.0"
author: The AtomVM Team
excerpt_separator: <!--more-->
---

We are thrilled to announce the release of AtomVM v0.7.0-alpha.0, the first pre-release on the road to v0.7.0 and by far the biggest AtomVM release to date. This alpha includes over two year of development work and introduces several groundbreaking features, including distributed Erlang, JIT compilation, big integer support, a comprehensive crypto module, and ETS.

<!--more-->

As an alpha release, some APIs may still evolve before a final release. For production deployments requiring maximum stability, we recommend staying on the [v0.6.x release branch](https://github.com/atomvm/AtomVM/tree/release-0.6). We encourage everyone to try this alpha and [report any issues](https://github.com/atomvm/AtomVM/issues), your feedback is invaluable.

**OTP-28 and OTP-29 are now supported.** This is a major improvement over v0.6.6, which did not include OTP-28 support. On macOS, both [Homebrew](https://brew.sh/) and [MacPorts](https://www.macports.org/) default to OTP-28 or later, so this release works out of the box with default system installations. You can use [asdf](https://asdf-vm.com/) or [mise](https://mise.jdx.dev) to manage multiple Erlang/OTP versions if needed.

## Highlights

### Distributed Erlang

AtomVM now supports the Erlang distribution protocol. AtomVM nodes running on microcontrollers can connect to standard BEAM nodes or other AtomVM instances, enabling use cases such as debugging an MCU from an OTP shell, coordinating multiple microcontrollers, or building mixed clusters. The release includes a pure Erlang `erl_epmd` client, support for external pids, ports, and refs, remote shell capabilities, and cookie-based authentication. The distribution protocol also supports alternative transport carriers beyond TCP/IP (e.g. SPI, I2C).

### JIT Compilation

AtomVM now supports four execution strategies: emulated (bytecode interpretation, the default), JIT (runtime native compilation), native ahead-of-time compilation, and a hybrid mode. The JIT compiler is itself written in Erlang, with supported architectures including RISC-V32, ARM v6-M, x86_64, and aarch64.

### Big Integers

Integer support has been extended to 256-bit (sign + 256-bit magnitude), with all arithmetic and bitwise operations working on big integers. Pattern matching and serialization via `binary_to_term` and `term_to_binary` is also supported.

### Cryptographic Operations

A comprehensive `crypto` module has been added, including key generation and exchange, digital signatures, streaming hashes and MACs, AEAD encryption, PBKDF2 key derivation, and Ed25519 support via optional libsodium integration.

### ETS

A limited but functional implementation of the OTP `ets` interface is now available, including support for `set`, `bag`, and `duplicate_bag` table types.

### Expanded Hardware Support

This release adds initial support for ESP32C5 and ESP32C61, WiFi for ESP32P4 (via esp-wifi-external), and 10 new STM32 families enabled by switching to the official ST HAL/LL SDK. ESP32 release images are now built with ESP-IDF v5.5.

### Elixir Improvements

Native `GenServer` and `Supervisor` support, `Function.ex` and `Protocol.ex` for Elixir 1.18, and a new `-DATOMVM_ELIXIR_SUPPORT=on` cmake flag for ESP32 builds.

## Upgrading

If upgrading from v0.6.x, please review the [UPDATING.md](https://github.com/atomvm/AtomVM/blob/v0.7.0-alpha.0/UPDATING.md) file. Notable breaking changes include the new `init:boot/1` entry point, ports replacing pids for native processes, `bsl` overflow checking, and the renamed C API `external_term` module.

## Binaries and Documentation

Pre-built binaries for ESP32 (13 chip variants, standard and Elixir flavors), Raspberry Pi Pico (pico, pico_w, pico2, pico2_w), and WebAssembly (Node.js and browser) are available on the [GitHub releases page](https://github.com/atomvm/AtomVM/releases/tag/v0.7.0-alpha.0). Users who need custom builds or STM32 support should consult the [Build Instructions](https://doc.atomvm.org/v0.7.0-alpha.0/build-instructions.html).

The complete [Getting Started Guide](https://doc.atomvm.org/v0.7.0-alpha.0/getting-started-guide.html) and full [Changelog](https://github.com/atomvm/AtomVM/blob/v0.7.0-alpha.0/CHANGELOG.md) are available in the documentation.

## Contributors

This release would not have been possible without our contributors. We also want to extend a special thank you to [Dashbit](https://dashbit.co/) and [Software Mansion](https://swmansion.com/) for having supported the development of AtomVM. Thank you!

_The AtomVM team_
