---
layout: post
title: Updates available for AtomVM Erlang tools
author: The AtomVM Team
excerpt_separator: <!--more-->
---

Updates for both [`atomvm_packbeam` 0.7.4](https://github.com/atomvm/atomvm_packbeam/releases/tag/0.7.4)
and [`atomvm_rebar3_plugin` 0.7.4](https://github.com/atomvm/atomvm_rebar3_plugin/releases/tag/0.7.4)
are available from [hex.pm](https://hex.pm/packages?search=atomvm&sort=updated_at) and on GitHub.
The `atomvm_packbeam` release brings support for OTP 28 to both tools. Numerous bugs have been
fixed in both tools. Full changelogs are linked in the respective release notes.

### Enhancements to `atomvm_rebar3_plugin`
- Add support for OTP application framework in AtomVM
- Improved pico_flash task
- Pack beams in test directory, for test profile
- Add port "auto" config - that enables auto detect of port for esp32 platform
- Replace uf2 creation code with upstream uf2tool
- Allow relative path for --external AVMs in packbeam task

### Bugs fixed in `atomvm_rebar3_plugin`
- Fix broken external includes in rebar.conf
- Fixed several instances of the app name not being set correctly in rebar3 new atomvm_app templates
- Added some missing license info

### Enhancements to `atomvm_packbeam`
- Support OTP-28 uncompressed literals

### Bugs fixed in `atomvm_packbeam`
- Fix bug in packbeam create
- Added some missing license info

_The AtomVM team_
