<properties pageTitle="Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure"
  description="Prevent, detect, and quickly remediate security policy and compliance violations using Intune, Active Directory, Code Push, and Azure."
  services=""
  documentationCenter=""
  authors="clantz" />

# Prevent, detect, and remediate security issues using Intune, Active Directory, Code Push, and Azure [Incomplete]
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions and even the most carefully crafted app can have unexpected security gaps. With that in mind, this document will outline some addition products that can help you detect, prevent, and quickly remediate security issues when they are found. 

## Prevent and Detect Issues
Preventing security issues really comes down to following guidence to reduce risk and using tools that help you identify problems before your app has even shipped. For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki) along with taking advantage of [Cordova platform security features](./cordova-security-platform.md), properly [authorizing your users](./cordova-security-auth.md), and taking steps to secure [data you store locally or send to or from services](./cordova-security-data.md). 

###Linting & Code Analysis
Static and dynamic code analysis tools can also help you identify problems and before you release your app. Linting/hinting tools like [JSHint](http://jshint.com/) for JavaScript, [FxCop](https://msdn.microsoft.com/en-us/library/bb429476.aspx) for C#, [OCLint](http://oclint.org/) for Objective-C (iOS), or the [Android SDK's lint](http://developer.android.com/tools/debugging/improving-w-lint.html) tool can help get you going. 

You can then step into more advanced static and dynamic code analysis tools like those offered by [HP Fortify](http://www8.hp.com/us/en/software-solutions/application-security/). Products like these are particularly useful if you are subject to compliance rules like PCI DSS.

JavaScript code typically makes up the bulk of your app's code and JSHint/JSLint or more advanced code analysis tools can be run on your JavaScript code directly (say via a [Gulp task](https://www.npmjs.com/package/gulp-jshint)). This makes it easy to add to any [Continous Integration](http://go.microsoft.com/fwlink/?LinkID=691186) you may be doing. 

C#, Objective-C, Java, and C++ code will normally be in plugins or the Cordova implementations for platforms like Android and iOS. You can get the most complete coverage of this type of code and then running a build for the platform in question. A fully formed project is present in the "platforms" folder in the project (ex: platforms/android).

###Device Compliance, Malware, and Jailbreak Detection witn Intune
When building an internal facing app, Mobile Device Management (MDM) and Mobile Application Managment (MAM) solutions like [Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) can detect Malware on Android and report jailbroken or rooted devices for iOS and Android. Intune's MAM capabilities can also be used stand alone and complement an existing MDM solution.

See the following articles for additional information:
- [Manage device compliance policies for Microsoft Intune](https://technet.microsoft.com/en-us/library/dn705843.aspx)
- [Create and deploy mobile app management policies with Microsoft Intune](https://technet.microsoft.com/en-us/library/mt627829.aspx)
- [Overview of the Microsoft Intune App SDK](https://msdn.microsoft.com/en-us/library/mt627767.aspx)

###Threat Detection
Even the most careful implementaiton can still have vulnerabilities so another aspect of security is detecting problems. In addition to the features Intune provides, Microsoft has a number of additional products that can help out in this space:

**[Azure Active Directory Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection/)** is an extremely useful service that can help detect anamolous activity against user accounts. When combined with an authentication solution that supports Azure AD like Azure Mobile Apps or the ADAL Cordova plugin and passing the authentication token to any service calls you make for verification on the server side, you will gain insight into potential brute force or compromised logins attacks.

See [Azure Active Directory Identity Protection documentaiton](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection/) along with the articles on [authenticating users](./cordova-security-auth.md) and [securing data at rest and over the wire](./cordova-security-data.md) for additional information.

**[Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/)** build's on top of Azure's world class certificatiosn and enables some powerful security features for your Virtual Machines and SQL databases including full support for auditing SQL servers, threat detection, transparent data encryption, and monitoring and notification about reccomented updates.   

**[Microsoft Advanced Threat Analytics](https://www.microsoft.com/en-us/server-cloud/products/advanced-threat-analytics/)** WORDS

**[Adallom](http://www.adallom.com)** WORDS

##Quickly Remediate
When you do identify a threat or security issue in an app that has been released, fixing it quickly can be critical particularly if you need to adhere to strict compliance rules in a regulated industry. 
 
###CodePush
Out of band updates via Code Push mean faster remediation of security flaws when found - no 10 day turnaround for iOS.

###Intune or a MDM solution
Using MDM can help you get updates to your enterprise users faster.

##Additional Security Topics
- [Learn about Cordova platform and app security features](./cordova-security-platform.md)
- [Secure and encrypt your Cordova app data at rest and over the wire](./cordova-security-data.md)
- [Authenticating users with Azure Mobile Apps or the Active Directory Authentication Library for CordovK](./cordova-security-auth.md)
- [Download samples from our Cordova Samples repository](http://github.com/Microsoft/cordova-samples)
