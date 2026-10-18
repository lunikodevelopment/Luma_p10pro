# 03: Modem

**Result: the modem is not in the device tree and cannot be reached from native
Linux. Without new driver work there are no calls, texts or mobile data.**

## Evidence

- `lga.dtsi`, `lga-pixel-common.dtsi` and `lga-blazer.dts` at
  `18686b294c6f6ba778cba66eccf01b9fcdf48988` (and unchanged in `next-20261016`)
  have no modem, PCIe, mailbox or shared-memory node. Searched in source.
- Even the mature Tensor G1 DT (`exynos/google/gs101.dtsi`, `gs101-pixel-common.dtsi`
  in `next-20261016`) has no modem node and no PCIe controller; the only
  "pcie" matches are a clock name and four regulator names.
- No upstream driver exists for Samsung Exynos ("Shannon") modems. On Android
  they are run by Samsung/Google's out-of-tree CP interface driver plus the
  proprietary RIL in Android userspace.

## Why the Fairphone 6 approach does not carry over

The Fairphone 6 port uses Qualcomm's stack: QRTR, `rmtfs`, `pd-mapper`, `tqftpserv`
and ModemManager's QMI plugin. None of those apply to a Samsung modem. There is
no QMI, no rmtfs, and ModemManager has no plugin for this IPC. Bringing it up
would mean reverse-engineering the CP boot sequence, the shared-memory IPC, and
the Samsung IPC protocol, then writing a kernel driver and a ModemManager
plugin. That is a research project on its own, not a port step.

## Hardware identity (to confirm on device)

Reported as Exynos 5400 from a leaked prototype's baseband string ("g5400").
Confirm on the owner's phone from stock: Settings > About phone > Baseband
version, and `fastboot getvar version-baseband`.
