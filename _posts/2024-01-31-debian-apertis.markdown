---
layout: post
title:  "Debian/Apertis work in January 2024"
date:   2024-01-31
categories: debian apertis
---

As part of my work at [Collabora](https://www.collabora.com/), I work among
other things on [Apertis](https://www.apertis.org/) which is a Debian derivative.
Because upstreaming our work is a key part of the [Apertis policy](https://www.apertis.org/policies/upstreaming/)
and more broadly to the [Collabora policy](https://www.collabora.com/about-us/open-source.html),
thus it allows me to contribute to Debian.

# Debian

Here is a list of contribution I have done during the previous month:

- [pipewire](https://tracker.debian.org/pkg/pipewire), the new default audio server
  of all major Linux distributions, was updated to [1.0.1](https://gitlab.freedesktop.org/pipewire/pipewire/-/releases/1.0.1).
  As usual, this new version was also uploaded to `bookworm-backports`
  and to `bullseye-backports-sloppy` allowing users of previous Debian releases
  to enjoy improvements of PipeWire.
- [wireplumber](https://tracker.debian.org/pkg/wireplumber), the session/policy
  manager of PipeWire, was updated to [0.4.81](https://gitlab.freedesktop.org/pipewire/wireplumber/-/releases/0.4.81)
  in the experimental repository making it easier for users to test the future
  0.5 version.
  Based on the [Debian Popularity Contest](https://qa.debian.org/popcon.php?package=wireplumber),
  some users are already playing with this new version. So, don't hesitate to
  join the party and to report any bugs to ensure the future 0.5 doesn't contain
  any regression.
- [roc-toolkit](https://tracker.debian.org/pkg/roc-toolkit), a library for
  real-time audio streaming over the network, this is an optional dependency of
  pipewire with a long-standing user request to build the pipewire module.
  roc-toolkit was finally packaged in Debian in November, but without all
  features enabled.   Indeed, it relies on openfec (which was not avaible in
  Debian) to   [recover lost data with unreliable networks](https://roc-streaming.org/toolkit/docs/internals/fec.html).
  This feature is now enabled in the last revision of the Debian package.
- [openfec](https://tracker.debian.org/pkg/openfec) was accepted in Debian
  after having clarified a license issue with the original author of the
  [LDPC codes](https://github.com/radfordneal/LDPC-codes/issues/7).

- [libcamera](https://tracker.debian.org/pkg/libcamera), an open source camera
  stack and framework allowing to control embedded cameras. It was updated to
  [0.2.0](https://git.libcamera.org/libcamera/libcamera.git/tag/?h=v0.2.0).
  Not sure yet how to handle the backport since this version introduce a SONAME
  change and the related pipewire changes are not backward compatible that means
  I will have to revert them to continue backporting new pipewire versions or I
  will have to backport libcamera0.2. Not sure if it's allowed by the Debian
  backport policy due to the SONAME change. Let's see how I will manage that
  without hurting our friends from [Mobian](https://mobian-project.org/) which
  are probably the main users of libcamera.
- [weston](https://tracker.debian.org/pkg/weston), the reference implementation
  of a Wayland compositor, was updated to [13.0.0](https://lists.freedesktop.org/archives/wayland-devel/2023-November/043326.html).
  For a nice summary by Marius Vlad see [his blog post](https://www.collabora.com/news-and-blog/news-and-events/weston-13-release-backends-consolidation.html).
- [wayland-protocols](https://tracker.debian.org/pkg/wayland-protocols), adds
  functionality not available in the Wayland core protocol, was updated to
  [1.33](https://lists.freedesktop.org/archives/wayland-devel/2024-January/043400.html).
  This new version is required by the next version of [Mutter](https://mutter.gnome.org/).

- [optee-os](https://salsa.debian.org/debian/optee-os), a kind of mini OS
  running in the [ARM TrustZone](https://www.arm.com/technologies/trustzone-for-cortex-m).
  `optee-os` is used to enable u-boot support for some boards like `SK-AM62`.
  It was initially rejected from Debian because of the embedded libraries with
  sensitive parts like `mbedtls` and `libtomcrypt` which obviously doesn't make
  the [life of the Debian Security Team easier](https://wiki.debian.org/UpstreamGuide#No_inclusion_of_third_party_code).
  I re-uploaded this package by trying to explain that optee-os is not a standard
  component of Debian, but a firmware which cannot use libraries from Debian.
  Let's hope that's enough to get it accepted in Debian.
- [onnxruntime](https://salsa.debian.org/deeplearning-team/onnxruntime), a
  cross-platform inference and training machine-learning accelerator, was also
  rejected from Debian because the upstream tarball contains a lot of datasets
  with [unclear license](https://github.com/microsoft/onnxruntime/issues/8963).
  See the unofficial Debian [ML-Policy](https://salsa.debian.org/deeplearning-team/ml-policy/-/blob/master/ML-Policy.rst)
  for a nice explanation. So, I excluded all datasets from the upstream tarball,
  at least until upstream clarifies their license, and re-uploaded the package
  to the NEW queue.
  Unfortunately, due to missing datasets I had to disable tests at build time
  for now. The packaged version is not the latest one since the latest version
  depends on random snapshot of other components that needs to be sync in Debian.
  But, because pytorch in Debian depends on other snapshot of the same set of
  components, it's not easy to sort out this mess.

- [svt-av1](https://tracker.debian.org/pkg/svt-av1), an AV1 encoder and decoder,
  was updat... heu no... I only updated its [salsa repo](https://salsa.debian.org/multimedia-team/svt-av1)
  to [1.8.0](https://gitlab.com/AOMediaCodec/SVT-AV1/-/releases/v1.8.0). I am a
  bit hesitant to update this package. Upstream doesn't care about ABI breakage
  without SONAME change [#2095](https://gitlab.com/AOMediaCodec/SVT-AV1/-/issues/2095)
  & [#2099](https://gitlab.com/AOMediaCodec/SVT-AV1/-/issues/2099) which leads
  to downstream issues [#1041302](https://bugs.debian.org/1041302) &
  [#1059068](https://bugs.debian.org/1059068). I don't have time to check if there
  is any ABI breakages for each new version and because I don't use this package,
  I am thinking about orphaning it :(.

# Apertis
Of course, most the Debian contributions above landed in Apertis (or will land
in the next Apertis releases). Here's some potentially interesting work I did in
Apertis:

- I worked on adding a new job in the [ci-package-builder](https://gitlab.apertis.org/infrastructure/ci-package-builder)
  pipeline allowing to test the freshly built packages directly on target devices
  (like `Raspberry Pi 4` or other [Apertis Reference Hardware](https://www.apertis.org/reference_hardware/))
  via our [LAVA](https://lava.collabora.dev/) system. As explained in the
  [documentation](https://www.apertis.org/guides/apertis-packages-testing/) I
  tried to make it partially compatible with [Debian autopkgtests](https://salsa.debian.org/ci-team/autopkgtest/-/blob/master/doc/README.package-tests.rst).

- Apertis has a [dashboard to monitor packages](https://infrastructure.pages.apertis.org/dashboard/),
  over the past months it was improved to check more and more potential issues.
  But now, it reports too much issues, most of them are false positives which make
  the true positives issues hidden. I created a bunch of merge requests to
  stop reporting wrong issues. It should now be easier to identify which packages
  need love.

- I updated [agl-compositor](https://gitlab.apertis.org/pkg/agl-compositor), this
  is the default [Wayland compositor](https://www.apertis.org/architecture/wayland_compositors/)
  used in Apertis. This compositor comes from the [Automotive Grade Linux](https://www.automotivelinux.org/)
  project. This new version is aligned to the last version of AGL aka
  [Prickly Pike v16.0.3](https://wiki.automotivelinux.org/agl-distro/release-notes#prickly_pike_v1603).
  It will probably be the final update because Apertis may switch to
  [Weston](https://wayland.pages.freedesktop.org/weston/) instead.

- I evaluated which parts of [opencv](https://tracker.debian.org/pkg/opencv) need
  to be disabled to make `opencv` compliant with
  [Apertis's License Expectations](https://www.apertis.org/policies/license-expectations/).
  OpenCV is the biggest computer vision library, so it seems obvious we need it in
  Apertis as our platform was initially developed for the automotive industry. I
  mean how cars can be more autonomous without vision :).

- Continue to homogenize the docker images used in our different gitlab pipelines,
  by moving away from Bullseye (`ci-package-builder`) and Ubuntu (`apertis-toolchain`)
  for Bookworm.

- And finally, last day of the month, I added an Apertis profile to `dpkg`
  allowing us to enable more default build flags. For now, only `-Wformat-overflow=2`
  and `-Wformat-truncation=2` are enabled in addition to the Debian ones. These flags
  should not have real effects except reporting more warnings during a build about
  potential issues with format strings.

No promise, but I will try to publish updates like this every month with the hope
it contains useful information, at least that should help me remember what I did :-D.

# References
- [Apertis](https://www.apertis.org/)
- [Debian](https://www.debian.org/)

--
Dylan
