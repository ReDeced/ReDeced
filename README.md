# ReDeced

Systems and low-level programming on [Void Linux](https://voidlinux.org). Mostly C++, Python, and Rust, with a fair amount of poking at the parts of a system that are supposed to just work.

**Goal: build a homelab.** Everything I learn ends up somewhere in that direction eventually — the things below are the pieces as they get made.

```
Void Linux · xbps · runit · Wayland (driftwm + noctalia, no DE)
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
| [driftwm](https://github.com/ReDeced/driftwm) | Fork of [malbiruk/driftwm](https://github.com/malbiruk/driftwm), an infinite-canvas Wayland compositor — windows live at native size on a 2D canvas and the display is a camera over it. No tiling, no workspaces. Rust, built on smithay, trackpad-first. The project I care about most. |
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

**glibc instead of musl.** Void ships musl by default; this install runs glibc 2.41. That is a deliberate choice rather than a default I never touched — it matters when working with glibc-linked C/C++ toolchains and prebuilt binaries, and it is worth knowing which one you are on before debugging a linking problem at 2am.

**The base is small.** About 1700 packages on a working desktop. Much of what I use is not in the base repository, and finding that out is the point — either the package exists in Void's repos or I build it. `linux-zen` for the kernel, `elogind` over `systemd-logind`, `fuzzel` for launching.

## No desktop environment

There is no DE here. The desktop is three pieces that I can read the source of, glued together by the standard interfaces rather than by a bundle:

| | |
|---|---|
| **[driftwm](https://github.com/ReDeced/driftwm)** | Wayland compositor — the window manager. Infinite canvas instead of a grid of workspaces. My fork, and the project I like working on most. |
| **noctalia-qs** | The shell: panel, launcher, notifications, quick settings. Written in QML on quickshell, built from source into `/usr/local` because Void packages quickshell but not this shell. |
| **xdg-desktop-portal** | Everything else. File chooser, screenshare, global shortcuts, the rest — all of it through the portal interfaces, so the compositor and the shell stay decoupled from individual apps. |

`XDG_CURRENT_DESKTOP=driftwm`, `XDG_SESSION_TYPE=wayland`. Multiple portal backends run in parallel (`gtk`, `wlr`, `gnome`, `termfilechooser`, and a locally built `luminous`), with `FileChooser` pointed at `termfilechooser` in `portals.conf`. Running several at once is the intended arrangement — backends advertise what they implement, and apps pick up whichever suits them.

driftwm itself starts `xdg-desktop-portal-gtk`, `xdg-desktop-portal-wlr`, and `xdg-desktop-portal` as child processes, so the desktop comes up without a session manager. A small watchdog in `~/.config/driftwm/scripts/` restarts the shell if it dies and stops when the compositor exits.

The reason this shape rather than a DE: a DE is a distribution of thousands of lines whose internals I cannot change, shipped with opinions I did not pick. Composing from a compositor, a shell, and the portal spec means every part is inspectable and replaceable — which is the GNU idea applied to a Wayland desktop. driftwm is GPL-3.0-or-later, and that freedom to run and modify the thing that draws your screen is the whole point of the exercise.

## Environment notes

`runit` is PID 1, so there is no systemd user manager — `driftwm-session` detects that and falls back to launching the compositor directly, which is exactly the path it takes here. Toolchains come from the Void repositories: gcc 14 and clang 21 side by side, cmake and ninja for builds, rustc from `rustup`.

Terminal is zsh with fzf, bat, and pyenv for juggling Python versions (3.11, 3.12, 3.14).

## Contact

Open to issues and pull requests on anything above. `gh` is set up, so the CLI works without fuss.

<!--
Void Linux: xbps, runit, elogind, glibc, Wayland.
No desktop environment — driftwm (compositor, my fork) + noctalia-qs (shell),
glued with xdg-desktop-portal. GNU philosophy in practice: every part readable.
Writing C, C++, Rust, Python. Systems programming, networking, security, ML plumbing.
Currently building toward a homelab.
-->