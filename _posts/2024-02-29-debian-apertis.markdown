---
layout: post
title:  "Debian/Apertis work in February 2024"
date:   2024-02-29
categories: debian apertis
---

As part of my work at [Collabora](https://www.collabora.com/), I work among
other things on [Apertis](https://www.apertis.org/) which is a Debian derivative.
Because upstreaming our work is a key part of the [Apertis policy](https://www.apertis.org/policies/upstreaming/)
and more broadly to the [Collabora policy](https://www.collabora.com/about-us/open-source.html),
thus it allows me to contribute to Debian.

# Debian

Here is a list of contribution I have done during the previous month:

- [pipewire](https://tracker.debian.org/pkg/pipewire), the new audio server
  of all major Linux distributions, was updated to [1.0.2](https://gitlab.freedesktop.org/pipewire/pipewire/-/releases/1.0.2)
  and quickly after to [1.0.3](https://gitlab.freedesktop.org/pipewire/pipewire/-/releases/1.0.3)
  fixing a bug with the alsa plugin.
  As usual, this new version was also uploaded to `bookworm-backports`
  and to `bullseye-backports-sloppy` allowing users of previous Debian releases
  to enjoy improvements of PipeWire.
- [wireplumber](https://tracker.debian.org/pkg/wireplumber), the session/policy
  manager of PipeWire, was updated to [0.4.82](https://gitlab.freedesktop.org/pipewire/wireplumber/-/releases/0.4.82)
  in the experimental repository making it easier for users to test the future
  0.5 version.

- [weston](https://tracker.debian.org/pkg/weston), the reference implementation
  of a Wayland compositor, tests at build time are now fatal for a subset of
  architectures (since they are known to fail on some arches: [GL#739](https://gitlab.freedesktop.org/wayland/weston/-/issues/739)).
  Moreover, they are now used as [autopkgtest](https://ci.debian.net/packages/w/weston/)
  allowing me to quickly detect new failures.
- [gfxreconstruct](https://tracker.debian.org/pkg/gfxreconstruct), tools for the
  capture and replay of graphics API calls, was updated to [1.0.2](https://github.com/LunarG/gfxreconstruct/releases/tag/v1.0.2).
- [vulkan-volk](https://tracker.debian.org/pkg/vulkan-volk),a meta-loader for
  Vulkan, was updated to [1.3.275.0](https://github.com/zeux/volk/releases/tag/vulkan-sdk-1.3.275.0)
  to sync with other Vulkan packages in Debian, especially since it's a new
  Build-Deps of [vulkan-tools](https://tracker.debian.org/pkg/vulkan-tools).

- [optee-os](https://tracker.debian.org/pkg/optee-os), a kind of mini OS
  running in the [ARM TrustZone](https://www.arm.com/technologies/trustzone-for-cortex-m),
  was accepted in Debian \o/ and was updated to [4.1.0](https://github.com/OP-TEE/optee_os/releases/tag/4.1.0)
  to align to the version of [optee-client](https://tracker.debian.org/pkg/optee-client)
  available in Debian/sid. I started importing changes from Apertis like
  building `optee-os` for `QEMU`. I still have to package development files
  allowing to build [Trusted Applications](https://www.apertis.org/concepts/op-tee/#trusted-applications).
- [arm-trusted-firmware](https://tracker.debian.org/pkg/arm-trusted-firmware),
  like for `optee-os`, I started importing change to enable `QEMU` support.

- [dav1d](https://tracker.debian.org/pkg/dav1d), a fast and small AV1 video
  stream decoder, was updated to [1.4.0](https://code.videolan.org/videolan/dav1d/-/releases/1.4.0).
  This version adds support of two new architectures: [RISC-V](https://wiki.debian.org/RISC-V)
  and [LoongArch](https://wiki.debian.org/LoongArch), adds new optimizations as usual
  and fixes [CVE-2024-1580](https://security-tracker.debian.org/tracker/CVE-2024-1580).
- [svt-av1](https://tracker.debian.org/pkg/svt-av1), an AV1 encoder and decoder,
  no news regarding the packaging, but some discussions happened upstream about
  the ABI breakage issue and the lack of soname change, it looks like this will
  be properly handled, see [GL#2099](https://gitlab.com/AOMediaCodec/SVT-AV1/-/issues/2099).

# Apertis
Of course, most the Debian contributions above landed in Apertis (or will land
in the next Apertis releases). Here's some potentially interesting work I did in
Apertis:

- I rebuilt all packages in Apertis v2025dev1 with the new Apertis profile in dpkg.
  This profile leads to new build failures. Because of the new default build
  flags `-Wformat-overflow=2` and `-Wformat-truncation=2`, new warnings are
  emitted during build but because upstream defines `-Werror`, all warnings
  are treated as errors what causes build to fail. As workaround, all these
  packages are now build with `-Wno-error=format-overflow` and
  `-Wno-error=format-truncation`.
- A Debian security update of [nodejs](https://tracker.debian.org/pkg/nodejs) was
  actually a new upstream version (i.e. from `18.13` to `18.19`), but unfortunately
  this version is not fully compatible since it led to the build failure of
  ~ 50 node packages which were not already fixed in Debian ([#1063530](https://bugs.debian.org/1063530)).
  We had to fix all of these packages in Apertis to avoid delaying the release
  of Apertis v2024.
- I started investigate the use of [scancode-toolkit](https://github.com/nexB/scancode-toolkit)
  to replace [scan-copyright](https://tracker.debian.org/pkg/libconfig-model-dpkg-perl)
  in our pipeline. Mainly because `scan-copyright` is a Debian tool without a
  real community behind it and sometimes detect a wrong license. `scancode` is
  well maintained upstream and integrated with [OSS Review Toolkit](https://github.com/oss-review-toolkit/ort)
  that is already used in Apertis to generate SBOM.
- I wrote a [small guide](https://www.apertis.org/guides/apertis-packages-lintian/)
  to enable `lintian` in Apertis `ci-package-builder` pipeline. I hope it will
  be useful to help newcomers with packaging.
- I finalized the support of testing Apertis [HMI](https://www.apertis.org/policies/images/#hmi)
  images for [Raspberry Pi 4](https://www.apertis.org/reference_hardware/rpi4_setup/)
  through [LAVA](https://www.apertis.org/guides/lava-apertis-testing/). Now,
  results of these tests are displayed at [qa.apertis.org](https://qa.apertis.org/).
- [opencv](https://tracker.debian.org/pkg/opencv) is now available in Apertis
  v2023 and v2025dev2 after having disabled features that doesn't match the
  [Apertis's License Expectations](https://www.apertis.org/policies/license-expectations/).

See you next month!

# References
- [Apertis](https://www.apertis.org/)
- [Debian](https://www.debian.org/)

--
Dylan
