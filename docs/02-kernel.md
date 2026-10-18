# 02: Kernel tree candidates

Checked 2026-10-18. All conclusions below were read from source at the pinned
commits, not from articles.

## Candidate A: upstream (Krzysztof Kozlowski's SoC tree, queued for Linux 7.4)

- Pull request: "arm64: google: New ARCH GOOGLE for v7.4", sent 2026-10-05 by
  Krzysztof Kozlowski.
- Tree: `https://git.kernel.org/pub/scm/linux/kernel/git/krzk/linux.git`, tag `google-lga-7.4`
- **Pinned commit: `18686b294c6f6ba778cba66eccf01b9fcdf48988`**
  ("arm64: dts: google: lga: fixup #size-cells DT warning", Peter Griffin, 2026-09-29)
- Base: `cee9395acd8043be0644b25c34bfa86623f2b935` (v7.3-rc1)
- Same DT blobs present unchanged in `next-20261016`
  (`2efb7a11f1aa4d2dc285fe2680cca22cb9f72f64`).
- Mainline (torvalds) was at v7.3-rc7 on this date, so none of this is in a release yet.

Fetched from the `gitlab.com/linux-kernel/linux-next` mirror because
git.kernel.org is not reachable from the research sandbox. The commit hash is
the identity, so this is the same object.

### What the Pixel 10 Pro DT contains

`arch/arm64/boot/dts/google/lga-blazer.dts` (blob `0894606624f8efc5a54c4cdb83c4d8f4c1160c8c`)
is 15 lines: a model string and a compatible, including `lga-pixel-common.dtsi`,
which only sets the serial alias and the 38.4 MHz oscillator. `lga.dtsi`
(blob `3e670e524c4c9fe2df1778605630dd1a50e631dd`) describes:

- 8 CPUs, cpu-map, idle states, PSCI power domains
- GICv3, arch timer, fixed 38.4 MHz clock
- one DesignWare UART, `status = "disabled"` (the bootloader enables it)
- reserved memory: ramoops, bootloader log, GSA log, ABL carveout

A search of `lga.dtsi` for `ufs|usb|pcie|modem|dsi|display` returns **zero
matches**. There is no clock controller, pinctrl, regulator/PMIC, I2C, SPI, UFS,
USB, PCIe, display, GPU or modem node. Upstream describes this DT as able to
"boot to an initramfs busybox shell".

### Drivers in linux-next for Laguna peripherals

- `drivers/usb/dwc3/dwc3-google.c` and `drivers/phy/phy-google-usb.c`
  (`google,lga-usb-phy`, bindings `google,lga-dwc3.yaml`, `google,lga-usb-phy.yaml`).
  Drivers exist, but there is **no DT node** for them and no clock/reset
  provider for Laguna, so USB cannot be enabled from upstream alone yet.
- No Laguna clock driver, pinctrl driver, UFS glue, PCIe glue, display or modem driver.

### Boot caveat

The cover letter (v2) says the Pixel bootloader treated a missing `ufs0` alias as
fatal; the placeholder node was dropped from the upstream DT during review and
Linaro's "pixelscripts" tooling adds the needed nodes at build time. Any
`fastboot boot` test of this kernel must reproduce that, with the script pinned
and recorded as a patch with provenance.

## Candidate B: postmarketOS

- No device package for `google-blazer`, `google-frankel` or `google-mustang`
  could be confirmed. gitlab.postmarketos.org blocks automated access and the
  gitlab.com pmaports mirror is frozen at November 2024, so this needs a manual
  check from a browser.
- The closest relative, `linux-postmarketos-gs101` (Pixel 6, Tensor G1), is a
  different SoC and does not apply.
- `wiki.postmarketos.org/wiki/Google_Pixel_10_Pro_(google-blazer)` returned a
  302 redirect to an unrelated domain (`wiki.nura.eco`). Not followed, not trusted.

**Consequence:** there is no known-good existing image to prove a boot with.
The first known-good boot would have to be the upstream busybox initramfs over
the serial console, as Peter Griffin and Brian Norris tested it.

## Candidate C: Google's Android kernel (GKI + pixel modules)

Has all drivers, but it is the Android kernel with out-of-tree vendor modules.
Useful as a **reference** for register layouts and DT values only. Not a base
for Luma: it fails the "native-Linux, no Android userspace" requirement for the
modem and most of the rest of the stack.

## Assessment

Mainline support for Tensor G5 is too immature for a usable Luma port today.
Even the older Tensor G1 (Pixel 6), after roughly three years of upstream work,
has storage and USB but no display or modem in mainline. Bringing up the
Pixel 10 Pro means writing (not porting) the clock, pinctrl, PMIC, UFS, USB DT,
display and more, and the modem has no driver to write against. See
[03-modem.md](03-modem.md).
