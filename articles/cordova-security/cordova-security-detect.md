<properties pageTitle="Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure"
  description="Prevent, detect, and quickly remediate security policy and compliance violations using Intune, Active Directory, Code Push, and Azure."
  services=""
  documentationCenter=""
  authors="clantz" />

# Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions. Like the web, Cordova is a very open platform and as a result it does not force you down a specific path that will always garuntee a secure app. 

For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki). However, given the increased capabilities Cordova apps are affored, it is important to limit your risk as much as possible. 

This document will outline some additional tips on ways to detect, prevent, and quickly remediate security issues when they are found. 

##Prevent/Detect Issues [Not authored yet]

1. Use a source code scanning solution - provide examples (HP Fortify, SonarQube)
2. Intune for MAM/MDM
3. Server side threat detection - Azure products like Trend Micro Azure Deep Security
4. Threat detection (Eg Adallom for custom apps, Lookout technologies)
5. Governance (eg Adallom for custom apps)
6. AD identity protection

Products like [Azure Rights Management](https://products.office.com/en-us/business/microsoft-azure-rights-management) and [Adallom](https://www.adallom.com/) particularly when coupled with [Azure AD Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection/) can also be used to ensure that only authorized users can access sensative data.

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
