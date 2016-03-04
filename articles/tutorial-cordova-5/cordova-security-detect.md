<properties pageTitle="Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure"
  description="Prevent, detect, and quickly remediate security policy and compliance violations using Intune, Active Directory, Code Push, and Azure."
  services=""
  documentationCenter=""
  authors="clantz" />

# Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions. Like the web, Cordova is a very open platform and as a result it does not force you down a specific path that will always garuntee a secure app. Instead provides a set of tools that you can use to lock down your app as appropriate. A forced lockdown approach can block critical scenarios and thus tends to have undesired results. For example, Windows 8.1's platform security features block the use of hosted content. This has been resolved in Windows 10 by instead providing options for locking down your app. Beyond platform features, Microsoft also has some additional options that you can use to further improve your overall app security. 

For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki). However, given the increased capabilities Cordova apps are affored, it is important to limit your risk as much as possible. This document will outline some of the security features that exist in Cordova and related Microsoft products along with some general best practices for improving the overall security of your app beyond what you may typically think about for web apps. 

##Prevent/Detect Issues [Not authored yet]

1. Use a source code scanning solution - provide examples (HP Fortify, SonarQube)
2. Intune for MAM/MDM
3. Server side threat detection - Azure products like Trend Micro Azure Deep Security
4. Threat detection (Eg Adallom for custom apps, Lookout technologies)
5. Governance (eg Adallom for custom apps)
6. AD identity protection

##Quickly Remediate [Not authored yet]
 
###CodePush
Out of band updates via Code Push mean faster remediation of security flaws when found - no 10 day turnaround for iOS.

###Intune or a MDM solution
Using MDM can help you get updates to your enterprise users faster.

##Additional Security Topics
- [Learn about Cordova platform and app security features](./cordova-security-platform.md)
- [Secure and encrypt your Cordova app data at rest and over the wire](./cordova-security-data.md)
- [Authenticating users with Azure Mobile Apps or the Active Directory Authentication Library for CordovK](./cordova-security-auth.md)
- [Download samples from our Cordova Samples repository](http://github.com/Microsoft/cordova-samples)
