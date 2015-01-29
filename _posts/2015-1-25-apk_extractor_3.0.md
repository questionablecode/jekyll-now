---
layout: post
title: APK Extractor (version 3.0)
developer: meher
sha1: d2ed7ff067c2dcfb8ae56cbde4670524064a9cde
md5: 8ee5e708a3469ce8c25c67c287526d47
appname: APK Extractor
appid: com.ext.ui
appversion: 3.0
appurl: https://play.google.com/store/apps/details?id=com.ext.ui
appicon: /assets/audit_resources/com.ext.ui/3.0/com.ext.ui.png
verdict: Installed
verdict-color: green
summary: This audit revealed nothing unexpected.
---

## Overt Description
From: [https://play.google.com/store/apps/details?id=com.ext.ui](https://play.google.com/store/apps/details?id=com.ext.ui)

> APK Extractor will extracts APK which is installed on android device and copy to SD card. Fast and easy to use.  Extracts all most all application, includes system applications. No ROOT access required. By Default Apk's will be saved in `/sdcard/ExtractedApks/`. Provided Search option to search applications. Compatible with latest version of Android 5.0. Saved apk format `AppName_AppPackage_AppVersionName_AppVersionCode.apk`.  Action bar, support from Android 2.2+ devices. Can extract multiple/all APK's by holding long click on any item.

## Analysis Time: 57 minutes
The total analysis time to audit this application was 57 minutes.  I did not count a few hours of time spent developing an analysis [utility for isolating code of well known advertisement and support libraries](https://github.com/questionablecode/library-analysis-toolbox) in this report. 

## Analysis Results
This app employs an in-app advertisement library called [StartApp](http://startapp.com/).  The app makes API calls to some fairly scary Android permissions (including READ_PRECISE_PHONE_STATE which is an undocumented permission), however the application only actually requests 4 permissions (READ_EXTERNAL_STORAGE, WRITE_EXTERNAL_STORAGE, INTERNET, and ACCESS_NETWORK_STATE). So the scary API calls to PHONE_CALLS, READ_PRECISE_PHONE_STATE READ_PROFILE, ACCESS_COARSE_LOCATION, and ACCESS_WIFI_STATE would fail.  Reading and writing to the external storage is a required permission for the app's main functionality (backing up APKs).  The StartApp library itself was responsible for all remaining API calls to permission-protected methods. Once I was able to isolate the primary logic of the application, everything seemed to be in order and there were no unexpected API calls.  The main app logic was simple, but took some time to decipher because it had been obfuscated.  In future updates I would be concerned about additional permissions being added to utilize the existing logic in the StartApp library that is currently under-privileged.


## Lessons Learned
1) Separating the main app's logic from the support and advertisement libraries was really a pain.  As an APK the Android support library and the advertisement libraries all come bundled together. Luckily I was able to use the [library-analysis-toolbox](https://github.com/questionablecode/library-analysis-toolbox) project I developed to identify the program elements of common library packages and just audit the main app functionality.  Here's a graph that shows the program slice of the app's main logic interacting with the more interesting Android SDK methods.

<script>
    jQuery(function($){
        $('#interesting-interactions').smoothZoom({
            width: 720,
            height: 250,
            
            /******************************************
            Enable Responsive settings below if needed.
            Max width and height values are optional.
            ******************************************/
            responsive: false,
            responsive_maintain_ratio: true,
            max_WIDTH: '',
            max_HEIGHT: ''
        });
    });
</script>
<img id="interesting-interactions" src="{{ site.baseurl }}/assets/audit_resources/com.ext.ui/3.0/interesting_interactions.png" alt ="Main App Logic"/>

2) The main app functionality was obfuscated so getting some context of what the app was doing was difficult.  I ended up looking at how the methods inside the main app logic interacted with the rest of the Android SDK.  From this I started to remove "boring" SDK methods (such as UI interactions), leaving only interactions with things like File IO.  In the future it would be nice to have the Android SDK subdivided already so I could ask how does the app interact with X or Y (not just the entire SDK).  After I identified interesting methods in the app's main logic, I wrote queries to see how the app used that method (within the apps main logic).  By looking at what Android SDK methods were called I was able to get an understanding of the obfuscated classes.  For instance here is a graph that shows that classes are responsible for selecting and saving the APK file.

<script>
    jQuery(function($){
        $('#save-apk-logic').smoothZoom({
            width: 720,
            height: 250,
            
            /******************************************
            Enable Responsive settings below if needed.
            Max width and height values are optional.
            ******************************************/
            responsive: false,
            responsive_maintain_ratio: true,
            max_WIDTH: '',
            max_HEIGHT: ''
        });
    });
</script>
<img id="save-apk-logic" src="{{ site.baseurl }}/assets/audit_resources/com.ext.ui/3.0/saveAPK.png" alt ="Save APK Logic" />

3) I found reading Jimple was kind of painful.  I preferred looking at call graphs to reading Jimple in most cases.  Jimple was too verbose and the logic that was probably originally one or two Java statements seemed to be dispersed over several lines of Jimple.  Inner classes were split out to separate Jimple files and there are no packages in Jimple (files are just fully qualified).  It was hard to mentally separate logical systems, so it would be nice to add package folders for each file back in as a form of package management.  In some cases, I suspect decompiled Java might be more handy to have around even if its horribly broken.  I googled around a bit and it seems that [Jimple can be converted back to Java](https://mailman.cs.mcgill.ca/pipermail/soot-list/2007-June/001245.html) (lossly of course) possibly even with some sort of mapping between the two.  If that doesn't work out, it might be possible to use the classes.jar file and use [JAD](http://jd.benow.ca/) to get a rough decompilation for those few spot inspections when it’s easier to read code.

## Toolchain Improvements
To aid in identifying support libraries and advertisement libraries I wrote a utility for discovering program elements in packages and sub-packages of well known libraries.  I found a list of top level package names for Android advertisement libraries in paper by Theodore Book, Adam Pridgen, and Dan Wallach called [Longitudinal Analysis of Android Ad Library Permissions](http://arxiv.org/pdf/1303.0857.pdf).  The  utility seemed useful for other projects as well so I factored it out into its own toolbox project called the [library-analysis-toolbox](https://github.com/questionablecode/library-analysis-toolbox).