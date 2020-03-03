---
layout: post
title:  "Debian LTS report for January 2020"
date:   2020-02-02 00:09:00 +0100
categories: debian lts
---
Hi,

January was my 1st month as a Debian LTS paid contributor.
I was assigned 8 hours and I spent 6.5 hours for the following:

[transfig](https://tracker.debian.org/pkg/transfig):
* Fixed [CVE-2018-16140](https://security-tracker.debian.org/tracker/CVE-2018-16140), [CVE-2019-14275](https://security-tracker.debian.org/tracker/CVE-2019-14275), [CVE-2019-19555](https://security-tracker.debian.org/tracker/CVE-2019-19555), tested, uploaded and released [DLA](https://www.debian.org/lts/security/2020/dla-2073).
* Investigate [CVE-2019-19746](https://security-tracker.debian.org/tracker/CVE-2019-19746) and [CVE-2019-19797](https://security-tracker.debian.org/tracker/CVE-2019-19797), but they were not reproducible in Debian.

[python-apt](https://tracker.debian.org/pkg/python-apt):
* Fixes for [CVE-2019-15795](https://security-tracker.debian.org/tracker/CVE-2019-15795) and [CVE-2019-15796](https://security-tracker.debian.org/tracker/CVE-2019-15796) were prepared and uploaded by its maintainer Julian Andres Klode. I published [DLA](https://www.debian.org/lts/security/2020/dla-2074).

[yara](https://tracker.debian.org/pkg/yara):
* Investigate [CVE-2019-19648](https://security-tracker.debian.org/tracker/CVE-2019-19648), still no upstream fix yet.

[sudo](https://tracker.debian.org/pkg/sudo):
* Fixed [CVE-2019-18634](https://security-tracker.debian.org/tracker/CVE-2019-18634), tested, uploaded and released [DLA](https://www.debian.org/lts/security/2020/dla-2094).

--
Dylan
