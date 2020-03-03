---
layout: post
title:  "Debian LTS report for February 2020"
date:   2020-03-03 10:48:49 +0100
categories: debian lts
---
Hi,

February was my 2nd month as a Debian LTS paid contributor.
I was assigned 4 hours (+ 1,5 hours from January) and I spent all of them for the following:

[rake](https://tracker.debian.org/pkg/rake):
* Fixed [CVE-2020-8130](https://security-tracker.debian.org/tracker/CVE-2020-8130), tested, uploaded and released [DLA-2120-1](https://www.debian.org/lts/security/2020/dla-2120).

[libimobiledevice](https://tracker.debian.org/pkg/libimobiledevice):
* Fixed [CVE-2016-5104](https://security-tracker.debian.org/tracker/CVE-2016-5104), tested, uploaded and released [DLA-2121-1](https://www.debian.org/lts/security/2020/dla-2121).

[libusbmuxd](https://tracker.debian.org/pkg/libusbmuxd):
* Fixed [CVE-2016-5104](https://security-tracker.debian.org/tracker/CVE-2016-5104), tested, uploaded and released [DLA-2122-1](https://www.debian.org/lts/security/2020/dla-2122).

[libmtp](https://tracker.debian.org/pkg/libmtp):
* Adapt patch from [DLA-1029-1](https://www.debian.org/lts/security/2017/dla-1029) (Wheezy) to fix [CVE-2017-9831](https://security-tracker.debian.org/tracker/CVE-2017-9831) and [CVE-2017-9832](https://security-tracker.debian.org/tracker/CVE-2017-9832) in Jessie. Still need to check whether some other parts need to be backported.

--
Dylan
