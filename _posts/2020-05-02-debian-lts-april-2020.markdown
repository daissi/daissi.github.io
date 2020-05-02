---
layout: post
title:  "Debian LTS report for April 2020"
date:   2020-05-02
categories: debian lts
---

Hi,

April was my 4th month as a [Debian LTS](https://wiki.debian.org/LTS) paid contributor,
a [Freexian](https://www.freexian.com/services/debian-lts.html)'s initiative.
I was assigned 6 hours and I spent all of them for the following:

[libmtp](https://tracker.debian.org/pkg/libmtp):
* Issued [DLA 2169-1](https://www.debian.org/lts/security/2020/dla-2169) for
  [CVE-2017-9831](https://security-tracker.debian.org/tracker/CVE-2017-9831) and
  [CVE-2017-9832](https://security-tracker.debian.org/tracker/CVE-2017-9832).
  The patch for these two issues was prepared last month, but I was waiting more testing
  before to issue the DLA.

[jbig2dec](https://tracker.debian.org/pkg/jbig2dec):
* Investigate CVE-2020-12268 and discuss with Ola regarding if it needs a DLA or not.
* Because CVEs are regularly open against jbig2dec, I wrote a DEP-8 test and I submitted the
  [patch](https://bugs.debian.org/959468).

[libsixel](https://tracker.debian.org/pkg/libsixel):
* Start to investigate open CVEs, then switch to discussion with Ola and Mike to determine
  whether it needs a DLA or not.
* Because CVEs are regularly open against libsixel, I wrote a DEP-8 test and did a
  [PR](https://salsa.debian.org/debian/libsixel/-/merge_requests/1).

Attended our first LTS meeting, the summary can be found [here](http://meetbot.debian.net/debian-lts/2020/debian-lts.2020-04-29-13.59.html).


Stay safe!

--
Dylan

<div style="text-align: center"><img src="/assets/images/Debian_LTS-2.png" width="200" /></div>
