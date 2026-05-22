---
layout: page
title: "Nix Flake Setup for ESP32 Development with AtomVM"
---

This tutorial shows you how to set up a reproducible development environment for ESP32 development with AtomVM using Nix Flakes. This approach ensures that all developers on your team have identical development environments, regardless of their host operating system.

## Prerequisites

Before starting, you need:

* [Nix package manager](https://nixos.org/download.html) installed with flakes enabled
* Basic familiarity with command line tools
* An ESP32 development board

## Enable Nix Flakes

[here you will find instructions to enable it](https://nixos.wiki/wiki/Flakes)

## Creating the Flake

Create a `flake.nix` file in your AtomVM project directory:

```nix
{
  description = "AtomVM ESP32 development environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-23.11";
    nixpkgs-unstable.url = "github:nixos/nixpkgs/nixos-unstable";
  };

  outputs =
    { nixpkgs, nixpkgs-unstable, ... }:
    let
      # Configurable AtomVM tag/branch for documentation dependencies
      atomvmTag = "main"; # Change to "release-0.6" or other tag as needed

      supportedSystems = [
        "x86_64-linux"
        "aarch64-linux"
        "x86_64-darwin"
        "aarch64-darwin"
      ];

      overlays = [ ];

      forEachSupportedSystem =
        f:
        nixpkgs.lib.genAttrs supportedSystems (
          system:
          f {
            pkgs = import nixpkgs-unstable {
              inherit system;
              config = {
                allowUnfree = true;
                permittedInsecurePackages = [ "mbedtls-2.28.10" ];
              };
              inherit overlays;
            };
            pkgs-stable = import nixpkgs {
              inherit system;
              config.allowUnfree = true;
            };
          }
        );
    in
    {
      devShells = forEachSupportedSystem (
        { pkgs, pkgs-stable }:
        {
          default = pkgs.mkShell {
            packages =
              with pkgs;
              [
                # Core build tools
                autoconf
                automake
                curl
                git

                # Erlang/Elixir toolchain
                beamMinimal27Packages.erlang
                beamMinimal27Packages.elixir_1_18
                beamMinimal27Packages.rebar3

                # ESP-IDF dependencies
                wget
                flex
                bison
                ccache
                libffi
                openssl
                dfu-util
                libusb1
                pkg-config
                python3
                python3.pkgs.pip
                python3.pkgs.virtualenv
                gperf

                # AtomVM build dependencies
                gcc-arm-embedded
                mbedtls_2
                zlib
                ninja
                doxygen
                graphviz
              ]
              ++ [
                pkgs-stable.cmake
              ];

            shellHook = ''
              # Create a local lib directory for symlinks
              mkdir -p .nix-shell-libs

              # Create symlinks for mbedtls with the expected soname
              ln -sf ${pkgs.mbedtls_2}/lib/libmbedtls.so.14 .nix-shell-libs/libmbedtls.so.10
              ln -sf ${pkgs.mbedtls_2}/lib/libmbedcrypto.so.7 .nix-shell-libs/libmbedcrypto.so.1
              ln -sf ${pkgs.mbedtls_2}/lib/libmbedx509.so.1 .nix-shell-libs/libmbedx509.so.1

              export LD_LIBRARY_PATH="$PWD/.nix-shell-libs:${
                pkgs.lib.makeLibraryPath [
                  pkgs.zlib
                  pkgs.mbedtls_2
                  pkgs.libusb1
                  pkgs.libffi
                  pkgs.openssl
                ]
              }:$LD_LIBRARY_PATH"

              # ESP-IDF setup
              export ESP_DIR="$PWD/.esp"
              export IDF_PATH="$ESP_DIR/esp-idf"

              # Clone and setup ESP-IDF if not already present
              if [ ! -d "$IDF_PATH" ]; then
                echo "Setting up ESP-IDF v5.5.2..."
                mkdir -p "$ESP_DIR"
                git clone --single-branch --branch v5.5.2 --recursive https://github.com/espressif/esp-idf.git "$IDF_PATH"
                cd "$IDF_PATH"
                ./install.sh esp32,esp32s2,esp32s3,esp32c2,esp32c3,esp32c6,esp32h2,esp32p4
                cd - > /dev/null
              fi

              # Source ESP-IDF export script to set up environment
              if [ -f "$IDF_PATH/export.sh" ]; then
                source "$IDF_PATH/export.sh" > /dev/null 2>&1
                echo "ESP-IDF environment activated"
                echo "You can now build AtomVM for ESP32!"
              fi

              # Install AtomVM doc requirements into ESP-IDF Python environment
              REQUIREMENTS_URL="https://raw.githubusercontent.com/atomvm/AtomVM/${atomvmTag}/doc/requirements.txt"
              echo "Installing AtomVM documentation dependencies (tag: ${atomvmTag})..."
              curl -sL "$REQUIREMENTS_URL" | pip install -q -r /dev/stdin
            '';
          };
        }
      );
    };
}
```

## Understanding the Flake

### Inputs

The flake uses two nixpkgs inputs:
- `nixos-23.11`: Stable release for reliable packages
- `nixos-unstable`: Latest packages for newer Erlang/Elixir versions

### Key Packages

**Erlang/Elixir Toolchain**
Change this to whatever versions are required for your build, AtomVM supports 1.19
```nix
beamMinimal27Packages.erlang
beamMinimal27Packages.elixir_1_18
beamMinimal27Packages.rebar3
```

You can switch to Erlang 28 by using `beamMinimal28Packages` instead.

**ESP-IDF Dependencies**
All the necessary packages for ESP-IDF are included: `flex`, `bison`, `gperf`, `python3`, etc.

**AtomVM Build Tools**
- `gcc-arm-embedded`: ARM cross-compiler
- `mbedtls_2`: TLS library (this requirement is for pico, but you should be able to change the version of mbedtls for ESP32 builds)
- `ninja`: Build system
- `cmake`: Build configuration

### Shell Hook Magic

The `shellHook` section performs several important tasks:

1. **MbedTLS Library Linking**: Creates symlinks for the correct mbedtls version that AtomVM expects
2. **Library Path Setup**: Configures `LD_LIBRARY_PATH` for all required shared libraries
3. **ESP-IDF Auto-Setup**: Automatically clones and installs ESP-IDF v5.5.2 on first run
4. **Environment Activation**: Sources the ESP-IDF environment variables

## Using the Flake

### Enter the Development Shell

```bash
cd your-atomvm-project
nix develop
```

The first time you run this:
- It will download all the packages
- Clone ESP-IDF v5.5.2
- Install toolchains for all ESP32 variants
- This may take 5-10 minutes depends on your machine.

Subsequent runs are instant!

## Add to .gitignore

Add these to your `.gitignore`:

```
# Nix build artifacts
.nix-shell-libs/

# ESP-IDF
.esp/
```

## Troubleshooting

### Permission Denied on USB Device

Add your user to the `dialout` group:

Then log out and back in.

### Library Version Errors

If you see mbedtls version errors, ensure the symlinks in `.nix-shell-libs` are created correctly. Exit and re-enter the shell.

## Benefits of This Approach

1. **Reproducible**: Everyone gets the same environment
2. **Isolated**: Doesn't pollute your system with development tools
3. **Documented**: The flake.nix serves as documentation
4. **Cross-platform**: Works on Linux and macOS
5. **Version Controlled**: Check the flake into git

## Next Steps

- Explore the [AtomVM documentation](https://doc.atomvm.org/latest/)
- Check out [example applications](https://github.com/atomvm/AtomVM/tree/main/examples)

Happy hacking with AtomVM and Nix!

Notes: this was tested in NixOS, if you find any bugs in this setup, please report it.
