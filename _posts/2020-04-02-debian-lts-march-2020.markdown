---
layout: post
title:  "Debian LTS report for March 2020"
date:   2020-04-02 10:48:49 +0100
categories: debian lts
---

Hi,

March was my 3rd month as a [Debian LTS](https://wiki.debian.org/LTS) paid contributor,
a [Freexian](https://www.freexian.com/services/debian-lts.html)'s initiative.
I was assigned 6 hours and I spent all of them for the following:

[libmtp](https://tracker.debian.org/pkg/libmtp):
* Finish the fix for [CVE-2017-9831](https://security-tracker.debian.org/tracker/CVE-2017-9831) and
  [CVE-2017-9832](https://security-tracker.debian.org/tracker/CVE-2017-9832), then I asked on the
  LTS mailing list for [testing](https://lists.debian.org/debian-lts/2020/03/msg00100.html).
  If no regression is reported, I will upload the fix on the 4th of April.

[libplist](https://tracker.debian.org/pkg/libplist):
* Fixed [CVE-2017-5209](https://security-tracker.debian.org/tracker/CVE-2017-5209), 
        [CVE-2017-5545](https://security-tracker.debian.org/tracker/CVE-2017-5545), 
        [CVE-2017-5834](https://security-tracker.debian.org/tracker/CVE-2017-5834), 
        [CVE-2017-5835](https://security-tracker.debian.org/tracker/CVE-2017-5835), 
        [CVE-2017-6435](https://security-tracker.debian.org/tracker/CVE-2017-6435), 
        [CVE-2017-6436](https://security-tracker.debian.org/tracker/CVE-2017-6436), 
        [CVE-2017-6439](https://security-tracker.debian.org/tracker/CVE-2017-6439) and
        [CVE-2017-7982](https://security-tracker.debian.org/tracker/CVE-2017-7982),
        tested, uploaded and released [DLA-2168-1](https://www.debian.org/lts/security/2020/dla-2168).

The majority of these CVE was fixed in Wheezy through [DLA-1029-1](https://www.debian.org/lts/security/2017/dla-1029),
 [DLA-811-1](https://www.debian.org/lts/security/2017/dla-811),
 [DLA-840-1](https://www.debian.org/lts/security/2017/dla-840) and
 [DLA-870-1](https://www.debian.org/lts/security/2017/dla-870),
 but not in Jessie! Now, it's fixed.

It was not easy to find free time to work on LTS, because of the lockdown due to the SARS-CoV-2.

Stay safe!

--
Dylan

<div style="text-align: center"><img src="/assets/images/Debian_LTS-2.png" width="200" /></div>
