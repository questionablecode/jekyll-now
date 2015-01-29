---
layout: default
---

# Overview

This site is dedicated to an exercise in examining everyday software with a skeptical mind, looking for bugs, malware, backdoors, unadvertised features, etc., and being forced to make a decision whether to use the software or not.  For the purposes of this project, Android applications on the [Google Play](https://play.google.com/store) store will be audited.  The verdict of the application audit corresponds with the analyst's decision to install or not install the application on his personal device.

## Motivation
A recent [audit of Uber's Android app](https://www.gironsec.com/blog/2014/11/what-the-hell-uber-uncool-bro/) revealed the application was abusing permissions to capture sensitive device information.  Earlier in 2012, several popular [iOS apps were found to be pilfering device contacts](https://www.techhive.com/article/250007/path_isnt_only_app_to_upload_store_address_book_data.html).  In early 2014, [Facebook was sharply criticized for requesting permissions to read SMS messages](https://techcrunch.com/2014/01/28/facebook-reading-android-users-texts-well-hold-on/) to perform two factor authentication because future Facebook application [updates may take advantage of existing permissions](https://www.secureandroidupdate.org/) for uses other than what they were originally granted.  Even an app with zero permissions can act maliciously, by leveraging the permissions of other apps on the system through [permission re-delegation attacks](https://www.usenix.org/conference/usenixsecurity11/permission-re-delegation-attacks-and-defenses).  

This experiment is the culmination of two years of experience working on DARPA's [Automated Program Analysis for Cybersecurity](https://www.darpa.mil/Our_Work/I2O/Programs/Automated_Program_Analysis_for_Cybersecurity_%28APAC%29.aspx) (APAC) program as a researcher at [Iowa State University](https://www.news.iastate.edu/news/2014/03/03/smartphonesecurity). We learned from APAC that malware can take many forms, but domain specific (aka business logic) issues are especially difficult to detect automatically because no formal models exist to check the software against.  With regard to permissions we would expect this type of malware to be blended well with legitimate functionality.  In some cases, a piece of [malware may be indistinguishable from a software bug](https://www.youtube.com/watch?v=3J4D7YJ1IE4) earning it a protective measure of plausible deniability.  Auditing for these types of issues is extremely difficult to achieve fully automatically with any sort of accuracy, but a combination man and machine system both enables the human to perform more complex analyses and reduces the time from the alternative of doing completely manual audits.  These realizations led to a comprehension driven analysis approach imbibed into [EnSoft's](https://www.ensoftcorp.com/) program analysis platform [Atlas](https://www.ensoftcorp.com/atlas/) and Iowa State University's approach to auditing software.  A demo video of Iowa State University's Security Toolbox and Atlas auditing a maliciously modified version of [ConnectBot](https://code.google.com/p/connectbot/) is shown below.

<center><iframe width="560" height="315" src="//www.youtube.com/embed/WhcoAX3HiNU" frameborder="0" allowfullscreen></iframe></center>

## Challenges
There are several key challenges to overcome in this experiment.

### Proprietary Code
The applications on the Google Play store are primarily proprietary code, so auditing the original source code of the application is not an option.  Even if the source were available, it would be challenging to prove that the source audited was the same source used to compile the resulting application.  This experiment will be dealing directly with the Android APK files downloaded from the Google Play store, so auditing code will require auditing Java bytecode.

### Vague Descriptions
The overt descriptions available on app stores are often vague and do not detail all functionality of the application.  Without a formal specification an analyst is forced to determine if undocumented functionality is malicious or benign or at least whether or not the functionality is warranted for the application.

### Complexity of Software
Software, including Android applications, is becoming enormously complex.  The number of non-nested control flow paths through a program is exponential, resulting in path feasibility problems for program analysis.  The growing number of readily available software libraries means this problem is only getting worse.

### Time
Developer time is expensive, so limiting the cost to a human is imperative.  Recent business trends to "release early and release often" means that application updates come at incredibly fast intervals for organizations that aim to audit every piece of software deployed in the organization.