---
title: "Part 2 - A Minimal Rust Kernel"
date: 2025-01-06T17:18:45+11:00
slug: part2
weight: 3
description: >
 Running through part 2 of making a rust os: [A Minimal Rust Kernel](https://os.phil-opp.com/minimal-rust-kernel/)
---

## Changes made before starting part 2 of the tutorial
Before starting part two I noticed that there were two issues I had to look at.

### Unsafe **no_mangle**
When attempting to build with the **thumbv7em-none-eabihf** target it was getting an error saying that `#[no_mangle]` needed
to be wrapped in an `unsafe` block. The original guide made no mention of this, however after a quick google search I was able
to find the relevant RFC to consider some attributes unsafe [here](https://rust-lang.github.io/rfcs/3325-unsafe-attributes.html).
I looked at the linked [PR](https://github.com/rust-lang/rfcs/pull/3325) and found that the change was made to nightly as
recently as Oct 31 2024 and since I was on a nightly version from the end of December 2024 it means it affected me.

I then made the change to the code like so
```rust {lineos=table,hl_lines=["6-9"]}
// main.rs
#![no_std]
#![no_main]
use core::panic::PanicInfo;

#[unsafe(no_mangle)]
pub extern "C" fn _start() -> ! {
    loop {}
}

#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```
### Rust Analyzer still referencing the **std** library
Rust analyzer was giving false-positive errors as it still references the **std** library despite the use of `#![no_std]`.
In order to fix this I would need to tell rust analyzer to not check on all targets. Since I use the Helix modal editor
for dev work I can create a local **languages.toml** file with any changes that are relevant for the projects.

After creating `.helix/languages.toml` in the base of my repo and adding the following contents rust analyzer no longer
reported errors based on using the **std** library.
```toml
[language-server.rust-analyzer.config.check]
allTargets = false
targets = "aarch64-apple-darwin"
```

## Actually working on Part 2
Thankfully while working on part two there were no issues involving my Nix setup or using a newer version.
I only had to add the `llvm-tools-preview` to my `rust-toolchain.toml` file so that the feature was added as a part of my
dev shell and then add `cargo-bootimage` to the list of Nix packages that should be available as well.

### Thoughts
- I had a look at the later versions of the bootloader library in case trying to use it would be worth it but as of right
now it seems too large a task. Will possibly look at it later when I have more experience with OS dev.
