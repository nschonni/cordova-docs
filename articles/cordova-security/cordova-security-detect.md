<properties pageTitle="Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure"
  description="Prevent, detect, and quickly remediate security policy and compliance violations using Intune, Active Directory, Code Push, and Azure."
  services=""
  documentationCenter=""
  authors="clantz" />

# Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions. Like the web, Cordova is a very open platform and as a result it does not force you down a specific path that will always garuntee a secure app. 

This document will outline some additional tips on ways to detect, prevent, and quickly remediate security issues when they are found. 

## Prevent and Detect Issues
Preventing security issues really comes down to following guidence to reduce risk and using tools that help you identify problems before your app has even shipped. For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki) along with taking advantage of [Cordova platform security features](./cordova-security-platform.md), properly [authorizing your users](./cordova-security-auth.md), and taking steps to secure [data you store locally or send to or from services](./cordova-security-data.md). 

###Linting & Code Analysis
Static and dynamic code analysis tools can also help you identify problems and before you release your app. Linting/hinting tools like [JSHint](http://jshint.com/) for JavaScript, [FxCop](https://msdn.microsoft.com/en-us/library/bb429476.aspx) for C#, [OCLint](http://oclint.org/) for Objective-C (iOS), or the [Android SDK's lint](http://developer.android.com/tools/debugging/improving-w-lint.html) tool can help get you going. 

You can then step into more advanced static and dynamic code analysis tools like those offered by [HP Fortify](http://www8.hp.com/us/en/software-solutions/application-security/). Products like these are particularly useful if you are subject to compliance rules like PCI DSS.

JavaScript code typically makes up the bulk of your app's code and JSHint/JSLint or more advanced code analysis tools can be run on your JavaScript code directly (say via a [Gulp task](https://www.npmjs.com/package/gulp-jshint)). This makes it easy to add to any [Continous Integration](http://go.microsoft.com/fwlink/?LinkID=691186) you may be doing. 

C#, Objective-C, Java, and C++ code will normally be in plugins or the Cordova implementations for platforms like Android and iOS. You can get the most complete coverage of this type of code and then running a build for the platform in question. A fully formed project is present in the "platforms" folder in the project (ex: platforms/android).

###Detecting Problems

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
