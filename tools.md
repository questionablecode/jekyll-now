---
layout: page
title: Tools
permalink: /tools/
---

This page details the tools and general setup used for software audits of Android applications.

## Analysis
At the core resides a program analysis platform called [Atlas](https://www.ensoftcorp.com/atlas/).  At this time the version of Atlas used to audit Android APKs is an experimental release of Atlas for Jimple, which allows analysis of Java bytecode.  On top of Atlas, is a custom open source "toolbox" containing utilities for auditing software.

- [https://github.com/questionablecode/android-audit-toolbox](https://github.com/questionablecode/android-audit-toolbox)
- [https://github.com/questionablecode/library-analysis-toolbox](https://github.com/questionablecode/library-analysis-toolbox)

More details to come soon...

## Downloading Applications
Getting a hold of the app to audit is another challenge in itself.  The following solutions have been used successfully during this project.

### APK Extraction
This so far is the most reliable method because it doesn't try to circumvent any sort of security checks.  Just download and install the app like normal, then use an app like [APK Extractor](https://questionablecode.org/apk_extractor_3.0/) or the [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) to pull the APK off the device.  This method has the downside of requiring you to install the app before you can audit it, however for paid apps it makes the most sense because the app is hidden behind a pay wall.  While the APK Extractor app does not require you to root your device, file transfers over ADB or a shell may require rooting the device first (opening yet another can of worms as far as security concerns go) if the device is not running a development build.

### App Store Crawlers
There are a few well tested app store crawlers for the Google Play store.  Since the Google Play store downloads applications through a custom protocol directly to a device, is is necessary for a crawler to impersonate or register a fake device.

A project to mass download apps on the Google Play store was undertaken in the [Playdrone](https://github.com/nviennot/playdrone) project. The crawler of choice for this project that is well suited for downloading a small number of selected applications is the [google-play-crawler](https://github.com/Akdeniz/google-play-crawler). 

*Warning:* It is always a risk that Google may ban the account used by a crawler (due to rate limits, suspicious activity, ect). It is advisable to use a Google account without any personal significance. Note that, as described ["A Measurement Study of Google Play"](http://viennot.com/playdrone.pdf), it is possible to create a new Google account without entering a current email address or phone number.

Occasionally for quick and dirty downloads, I've used [https://apps.evozi.com/apk-downloader/](https://apps.evozi.com/apk-downloader/), which is a site that runs an APK crawler with a set of accounts and offers a limited number of app downloads a day.  If you use this service I would highly recommend using an [advertisement blocker](https://adblockplus.org/) as well as safe browsing habits since the pop-ups are quite aggressive/suspicious.