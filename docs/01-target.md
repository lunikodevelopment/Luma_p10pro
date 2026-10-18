# 01: Target identification

| Field | Value | Verified how |
|---|---|---|
| Marketing name | Google Pixel 10 Pro | |
| Device codename | `blazer` | Upstream DT `lga-blazer.dts`, `model = "Blazer"` (linux-next, see 02-kernel.md) |
| Board compatible | `google,lga-blazer`, `google,lga` | Same file, read in source |
| Siblings | `frankel` (Pixel 10), `mustang` (Pixel 10 Pro XL). Pixel 10 Pro Fold is a different device and not covered | Upstream DT Makefile |
| SoC | Google Tensor G5, codename Laguna, DT prefix `lga` | `arch/arm64/boot/dts/google/lga.dtsi` |
| CPU | 2x Cortex-A520, 5x Cortex-A725, 1x Cortex-X4 | CPU nodes in `lga.dtsi`, read in source |
| GPU | Imagination PowerVR (DXT family) | Press coverage only; **not verified**, no GPU node upstream |
| Modem | Samsung Exynos 5400 (reported) | Leaked prototype baseband string "g5400"; **verify on device** with the baseband version in Settings or `getprop gsm.version.baseband` on stock |
| Exact SKU / variant | **To record from the device** | Back of box / Settings > About phone > Model, plus `fastboot getvar product` and `fastboot getvar hw-revision` |

## Things to record from the device before any work (owner runs, read-only)

These commands only read. They still go through the owner, one at a time.

```
fastboot getvar product
fastboot getvar hw-revision
fastboot getvar version-bootloader
fastboot getvar version-baseband
fastboot getvar current-slot
fastboot getvar unlocked
fastboot getvar anti
```

And on stock Android: Settings > About phone > Android version > Build number
(the exact build ID, e.g. `BP4A.xxxxxx.xxx`). The build ID decides which factory
image is "the" stock image for this phone.

Note: upstream says it supports only mass-production **B0 silicon** (Peter
Griffin's cover letter; A0/EVT boards are best-effort). A retail phone should be B0.

## Bootloader unlock procedure (standard Pixel flow; verify on this unit)

1. Back up everything. Unlocking wipes user data.
2. Settings > About phone > tap Build number 7 times.
3. Settings > System > Developer options > enable **OEM unlocking**. If greyed out,
   the unit is carrier-locked (some US carrier SKUs) and **cannot** be unlocked. Stop there.
4. Reboot to bootloader (`adb reboot bootloader` or Power + Volume Down).
5. `fastboot flashing unlock` and confirm on the phone.

Never run `fastboot flashing lock` on this port. Relocking with non-stock images
on the device can brick it.

## Anti-rollback warning (important)

Google's factory image page states that the **May 2026** update for Pixel 10,
10 Pro, 10 Pro XL and 10 Pro Fold contains a bootloader update that raises the
anti-rollback version: after it, older Android 16 builds cannot be flashed or
booted. It also warns that after the first boot of that update, the *inactive*
slot still holds an older bootloader; if the active slot is later flashed with
something that won't boot, the device can fall back to that slot and end up
unbootable. Google's fix: after booting the May 2026 (or newer) build, sideload
the matching full OTA so **both slots** hold a bootable, current image.

For this port that means:

- Update stock to the current release and sideload its full OTA **before** any
  experiment, so both slots are current and consistent.
- Only ever factory-flash a build at or above the device's current bootloader.
- Our own kernel goes through `fastboot boot` (RAM only) first, which leaves both
  slots untouched.
