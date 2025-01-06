---
title: "Part 1 - A Freestanding Rust Binary"
date: 2025-01-06
weight: 2
slug: part1
description: >
  Running through the first part with [A Freestanding Rust Binary](https://os.phil-opp.com/freestanding-rust-binary/)
---

## Rust Analyzer and **no_std**
Starting this guide everything appears to be the working as intended with the guide. So far the only thing worth noting is that
the Rust Analyzer is throwing errors regarding the use of the custom panic implementation as it still refers to the standard
library despite the use of `#![no_std]`.

Looking online there does appear to be some possible fixes and I may apply them if none of what is applied in future tutorials
fixes it.

## Nix issues with a different build targets
For my developer environment I use nix-direnv with a flake.nix which will install and setup any special packages as well as the
Rust version I want.

Previously I was using the nightly version of Rust in order to use `no_std` however I couldn't install extra targets though
the **flake.nix** directly. Luckily the flake was already setup to install Rust according to a supplied **rust-toolchain.toml**
file.

So I created the following **rust-toolchain.toml** file and committed it to git so Nix could properly detect it.
```toml
[toolchain]
channel = "nightly-2024-12-20"
components = ["rust-src", "rustfmt", "clippy"]
targets = ["thumbv7em-none-eabihf"]
profile = "default"  
```

When that was complete direnv reloaded and I was able to cross compile the existing example code.
