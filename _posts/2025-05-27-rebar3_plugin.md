---
layout: post
title: Emergency bug fix release `atomvm_rebar3_plugin 0.7.5` has been published
author: The AtomVM Team
excerpt_separator: <!--more-->
---

We have released [atomvm_rebar3_plugin 0.7.5](https://github.com/atomvm/atomvm_rebar3_plugin/releases/tag/0.7.5)
as an emergency bug fix for the broken dependencies when pulling
[atomvm_rebar3_plugin from hex.pm](https://hex.pm/packages/atomvm_rebar3_plugin).

This was due to the uf2tool dependency source from the GitHub tag rather than the hex package.
Apologies to anyone who tried to use the `atomvm_rebar3_plugin 0.7.4` release from hex.pm.

There are no other changes or feature additions, just a working package this time ;-).

_- the AtomVM team_
