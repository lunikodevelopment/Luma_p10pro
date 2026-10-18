# Sources

Verified in source = read the file at a pinned commit. Lead = used to know
where to look, not trusted on its own.

| Source | Used for | Trust |
|---|---|---|
| linux-next `next-20261016` (`2efb7a11f1aa4d2dc285fe2680cca22cb9f72f64`), via gitlab.com/linux-kernel/linux-next mirror | DT and driver contents | Verified in source |
| krzk/linux tag `google-lga-7.4`, commit `18686b294c6f6ba778cba66eccf01b9fcdf48988` | Pinned kernel candidate | Verified in source |
| Pull request "arm64: google: New ARCH GOOGLE for v7.4", 2026-10-05 (lkml.rescloud.iu.edu/2610.0/08173.html) | Tag, base and head commit | Lead, confirmed by fetching the commit |
| "[PATCH v2 0/5] Add Laguna/Tensor G5 SoC and Frankel, Blazer & Mustang boards", Peter Griffin, 2026-07-22 | B0 silicon, `ufs0` bootloader caveat, "busybox shell" scope | Lead (maintainer statement) |
| Roy Luo, "Add Google Tensor SoC USB controller support" (LWN 1049440) | USB driver origin | Lead, driver confirmed in linux-next |
| developers.google.com/android/images | Anti-rollback warning for Pixel 10 | Official vendor statement |
| Android Authority, "pixel-10-modem" | Exynos 5400 modem | Lead only; verify on device |
| Phoronix, Xenospectrum articles on Linux 7.4 | Where to look | Lead only |
| pkgs.postmarketos.org `linux-postmarketos-gs101` | Nearest pmOS relative (Pixel 6) | Lead |
