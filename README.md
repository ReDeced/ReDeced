# ReDeced

Systems and low-level programming on [Void Linux](https://voidlinux.org). Mostly C++, Python, and Rust, with a fair amount of poking at the parts of a system that are supposed to just work.

**Goal: build a homelab.** Everything I learn ends up somewhere in that direction eventually — the things below are the pieces as they get made.

```
Void Linux · xbps · runit · Wayland (LXQt + driftwm)
gcc 14 · clang 21 · cmake · ninja · rustc 1.98 · python 3.14 · zsh
```

## What I work on

**Native and systems code.** Linux headers in C and C++, CMake build systems, OpenGL shaders, Wayland clients. Reading the protocol docs and writing against them directly tends to be more satisfying than picking up a framework for it.

**Network and security.** Packet capture with libpcap, binary protocols over UNIX sockets, end-to-end encryption design. Comfortable in the parts of a system where the answer is a syscall rather than an import.

**ML as applied plumbing.** PyTorch for sequence models over streaming data, training pipelines that survive the data being ten times larger than memory. Mostly infrastructure rather than model architecture.

## Projects

| | |
|---|---|
| [NetAI](https://github.com/ReDeced/NetAI) | Learning traffic representations on unlabeled data. C++/libpcap capture → fixed 23-byte binary protocol over a UNIX socket → sessionization → 9 features per packet → 128-packet windows → sharded storage → LSTM. The point is that it needs no labels. |
| [direct_chat](https://github.com/ReDeced/direct_chat) | Messenger with end-to-end encryption. Curve25519 identity keys, private key never leaves the device, a per-chat symmetric key wrapped separately for every participant. Server stores ciphertext and forwards it back unchanged — it has no way to read any of it. |
| [driftwm](https://github.com/ReDeced/driftwm) | Fork of malbiruk/driftwm, an infinite-canvas Wayland compositor. Rust, QML, GLSL. Trackpad-first. |
| [hamster-capture](https://github.com/ReDeced/hamster-capture) | Face capture pipeline and emotion classifier. ResNet-50 backbone with a custom head, OpenCV, live GUI capture feeding a worker pool. |
| [OpenGL-Perlin-noise-renderer](https://github.com/ReDeced/OpenGL-Perlin-noise-renderer) | Perlin noise rendered on the GPU. C, C++, CMake, shaders. |
| [team-lead-simulator](https://github.com/ReDeced/team-lead-simulator) | Rust logic with a web frontend. |
| [TrainsManager](https://github.com/ReDeced/TrainsManager) · [arraylist](https://github.com/ReDeced/arraylist) | C++ with Python alongside. |
| [FilmsTGBot](https://github.com/ReDeced/FilmsTGBot) · [MusicTGBot](https://github.com/ReDeced/MusicTGBot) | Telegram bots, Python. |
| [BortovoyComputer](https://github.com/ReDeced/BortovoyComputer) | Python and batch scripting. |

## Why Void

Not an aesthetic argument — it just removes the layers I would otherwise be debugging through.

**runit instead of systemd.** PID 1 is a small C program and a directory of services you can read in one sitting. When something behaves strangely, the answer is usually in `/etc/runit/service` and you can see it. No journaling to interpret, no socket activation to trace, no unit files to reverse-engineer.

**xbps instead of pacman or apt.** Small, fast, and the scripts do one thing. Repository configuration is plain text, which makes it easy to see what a third-party repo actually touches on your system. The nonfree and multilib repos are enabled here — Void being independent-spirit-only about what it ships is a feature, not a gap.

**glibc instead of musl.** Void ships musl by default. This install runs glibc 2.41, which matters when working with glibc-linked C/C++ toolchains and prebuilt binaries. Worth knowing which one you are on before debugging a linking problem at 2am.

**The base is small.** About 1700 packages on a working desktop. Much of what I use is not in the base repository, and finding that out is the point — either the package exists in Void's repos or I build it. `linux-zen` for the kernel, `elogind` over `systemd-logind`, `fuzzel` for launching, `waybar` and `swaync` for the panel and notifications.

## Environment notes

Running LXQt on Wayland with [driftwm](https://github.com/ReDeced/driftwm) as the compositor — which is also why the fork exists. Void's Wayland support is a first-class target rather than something bolted on, and LXQt on `qtwayland` behaves well against a compositor I can actually read the source of.

Terminal is zsh with fzf, bat, and pyenv for juggling Python versions. Toolchains come from the Void repositories — gcc 14 and clang 21 side by side, cmake and ninja for builds, rustc from `rustup`.

## Contact

Open to issues and pull requests on anything above. `gh` is set up, so the CLI works without fuss.

<!--
Void Linux: xbps, runit, elogind, musl/glibc, Wayland
Writing C, C++, Rust, Python. Systems programming, networking, ML plumbing.
Currently building toward a homelab.
-->