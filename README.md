# Luma Mobile on the Google Pixel 10 Pro

Port notes, patches (with provenance), build configuration and write-up drafts
for running Luma Mobile (Fedora-based) on the Pixel 10 Pro.

**Current status (2026-10-18): research only. Nothing has touched a phone.**

The short version: native-Linux support for the Pixel 10 Pro's SoC (Google
Tensor G5, "Laguna") is at the very first step. The upstream device tree boots
to a busybox shell from RAM over a serial console and describes nothing else:
no storage, display, touch, USB, Wi-Fi, audio or modem. The modem is a Samsung
Exynos part with no mainline Linux driver at all, so there is no path today to
calls, texts or mobile data from native Linux. See
[docs/02-kernel.md](docs/02-kernel.md) and [docs/03-modem.md](docs/03-modem.md).

| Document | What it covers |
|---|---|
| [docs/01-target.md](docs/01-target.md) | Model, codename, SoC, unlock procedure, anti-rollback warning |
| [docs/02-kernel.md](docs/02-kernel.md) | Kernel tree candidates, pinned commits, what the DT actually contains |
| [docs/03-modem.md](docs/03-modem.md) | Modem hardware and whether Linux can reach it |
| [docs/04-stock-recovery.md](docs/04-stock-recovery.md) | Getting back to stock, safety rules for every flash |
| [firmware/HASHES.md](firmware/HASHES.md) | Factory image and firmware hashes (filled in on download) |
| [docs/sources.md](docs/sources.md) | Every source used, and how much it was trusted |

## Ground rules for this port

- Pin exact commits, never branch heads. Record provenance for every patch in `patches/`.
- Firmware comes only from Google's factory image for the exact build, with hashes recorded.
- RAM-only `fastboot boot` before any flash. Only the partitions the boot needs are written.
- Never repartition, relock, write `vbmeta`, or touch `persist`, `modemst*`, `fsg` or equivalents.
- Every command that writes to the phone is approved individually by the owner and run one at a time.
- Nothing core depends on Android userspace. No identifier spoofing.
