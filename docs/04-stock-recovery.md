# 04: Getting back to stock, and safety rules

## Before anything touches the phone

1. Record the device facts listed in [01-target.md](01-target.md).
2. Update stock Android to the current release. Sideload the matching full OTA so
   both slots hold the same current build (Google's anti-rollback advice for
   Pixel 10 after the May 2026 bootloader update).
3. Download the factory image **for that exact build ID** from
   https://developers.google.com/android/images (section "blazer"). Copy
   Google's published SHA-256 into [../firmware/HASHES.md](../firmware/HASHES.md),
   then compute it locally with `sha256sum` and record both. They must match.
4. Keep the zip and the unpacked images. This is the only firmware source for the port.

## Going back to stock

Use the factory image's own `flash-all.sh` from that exact zip, or
Google's Android Flash Tool, with "wipe" as needed. Never flash a build older
than the device's current bootloader (anti-rollback).

## Rules for port work

- `fastboot boot <image>` (RAM only) before any flash. It leaves both slots untouched.
- Never: `fastboot flashing lock`, repartitioning, `flash vbmeta*`,
  `--disable-verity/--disable-verification`, or writing `persist`, `modem*`,
  `efs`, `modemst*`, `fsg` or any partition not needed for the boot.
- Every command that writes to the phone is approved by the owner and run one at a time.
