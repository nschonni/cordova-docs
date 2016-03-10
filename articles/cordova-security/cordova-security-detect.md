<properties pageTitle="Prevent, detect, and remediate security issues using Intune, Active Directory, CodePush, and Azure"
  description="Prevent, detect, and quickly remediate security policy and compliance violations using Intune, Active Directory, CodePush, and Azure."
  services=""
  documentationCenter=""
  authors="clantz" />

# Prevent, detect, and remediate security issues using Intune, Active Directory, CodePush, and Azure 
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions and even the most carefully crafted app can have unexpected security gaps. With that in mind, this document will outline some addition products that can help you detect, prevent, and quickly remediate security issues when they are found. 

## Prevent and detect issues
Preventing security issues really comes down to following guidence to reduce risk and using tools that help you identify problems before your app has even shipped. For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki) along with taking advantage of [Cordova platform security features](./cordova-security-platform.md), properly [authorizing your users](./cordova-security-auth.md), and taking steps to secure [data you store locally or send to or from services](./cordova-security-data.md). 

###Find issues with linting & code analysis tools
Static and dynamic code analysis tools can also help you identify problems and before you release your app. Linting/hinting tools like [JSHint](http://jshint.com/) for JavaScript, [FxCop](https://msdn.microsoft.com/en-us/library/bb429476.aspx) for C#, [OCLint](http://oclint.org/) for Objective-C (iOS), or the [Android SDK's lint](http://developer.android.com/tools/debugging/improving-w-lint.html) tool can help get you going. 

You can then step into more advanced static and dynamic code analysis tools like those offered by [HP Fortify](http://www8.hp.com/us/en/software-solutions/application-security/). Products like these are particularly useful if you are subject to compliance rules like PCI DSS.

JavaScript code typically makes up the bulk of your app's code and JSHint/JSLint or more advanced code analysis tools can be run on your JavaScript code directly (say via a [Gulp task](https://www.npmjs.com/package/gulp-jshint)). This makes it easy to add to any [Continous Integration](http://go.microsoft.com/fwlink/?LinkID=691186) you may be doing. 

C#, Objective-C, Java, and C++ code will normally be in plugins or the Cordova implementations for platforms like Android and iOS. You can get the most complete coverage of this type of code and then running a build for the platform in question. A fully formed project is present in the "platforms" folder in the project (ex: platforms/android).

###Device compliance, malware, and jailbreak detection with Intune
When building an internal facing app, Mobile Device Management (MDM) and Mobile Application Management (MAM) solutions like **[Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/)** can detect Malware on Android and report jailbroken or rooted devices for iOS and Android. Intune's MAM capabilities can also be used stand alone and complement an existing MDM solution.

See the following articles for additional information:
- [Manage device compliance policies for Microsoft Intune](https://technet.microsoft.com/en-us/library/dn705843.aspx)
- [Create and deploy mobile app management policies with Microsoft Intune](https://technet.microsoft.com/en-us/library/mt627829.aspx)
- [Decide how to prepare apps for mobile application management with Microsoft Intune](https://technet.microsoft.com/en-us/library/mt631425.aspx). 

Note that Intune provides two solutions for enabling its MAM features: an app wrapping tool and an app SDK. The app wrapping tool is generally available. Intune App SDK Cordova plugin is [currently in beta](https://blogs.msdn.microsoft.com/visualstudio/2015/11/18/announcing-the-intune-app-sdk/). See [Microsoft Intune documentaiton](https://technet.microsoft.com/en-us/library/mt631425.aspx) for a comparison of the features provided by the app wrapping tool and the App SDK. If you are interested in the beta plugin, see the [announcement blog post](https://blogs.msdn.microsoft.com/visualstudio/2015/11/18/announcing-the-intune-app-sdk/) for more details getting access and stay tuned for the upcoming GA announcement. Otherwise see the Intune documentation on the [Android](https://technet.microsoft.com/en-us/library/mt147413.aspx) and [iOS](https://technet.microsoft.com/en-us/library/dn878028.aspx) app wrapping tools for more information.

###Threat detection
Even the most careful implementation can still have vulnerabilities so another aspect of security is detecting problems. In addition to the features Intune provides, Microsoft has a number of additional products that can help out in this space:

**[Azure Active Directory Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection/)** is an extremely useful service that can help detect anomalous activity against user accounts. When combined with an authentication solution that supports Azure AD like Azure Mobile Apps or the ADAL Cordova plugin and passing the authentication token to any service calls you make for verification on the server side, you will gain insight into potential brute force or compromised logins attacks.

See [Azure Active Directory Identity Protection documentaiton](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection/) along with the articles on [authenticating users](./cordova-security-auth.md) and [securing data at rest and over the wire](./cordova-security-data.md) for additional information.

**[Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/)** build's on top of Azure's world class certifications and enables some powerful security features for your Virtual Machines and SQL databases including full support for auditing SQL servers, threat detection, transparent data encryption, and monitoring and notification about recommended updates.   

**[Microsoft Advanced Threat Analytics](https://www.microsoft.com/en-us/server-cloud/products/advanced-threat-analytics/)** is an on-premises product that provides an intelligent and adapting mechanism for [detecting potential threats across services and servers in a datacenter](https://technet.microsoft.com/en-us/library/dn707706.aspx). This is particularly useful in the mobile context when your app needs to access services housed within a corporate datacenter rather than Azure. 

##Quickly remediate issues
When you do identify a threat or security issue in an app that has been released, fixing it quickly can be critical particularly if you need to adhere to strict compliance rules in a regulated industry. 
 
###CodePush
One of the more frustrating aspects of mobile apps particularly for those that come from a web based background is that there are delays associated with getting app updates in the hands of customers. These can be driven by either app store submission delays which can be up to 10 days for iOS or simply the fact that not every user actually updates their apps when prompted.

**[CodePush](http://microsoft.github.io/code-push/)** is service that is designed to resolve this problem for Android and iOS based devices. The service allows you to update local static content (HTML, CSS, images) and JavaScript code in your app without an app store submission. The service supports both Cordova and React Native based apps through the use of a plugin.  You can even wire it into continuous delivery flows via a [Visual Studio Team Services extension](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.code-push).

In the context of security this is extremly useful for quickly remediating security vulnerabilities. Using the service you can force updates to end users (or prompt if you prefer) and ensure everyone is on the same version of the code with the fix applied. Any fixes you can do in JavaScript code can then be deployed without an app store submission which can drop a 10 day turnaround for iOS to minutes!

See the **[CodePush Getting Started Guide](http://microsoft.github.io/code-push/docs/getting-started.html)** for information on getting up and running.

###Intune or a MDM solution
Internal facing mobile apps are becoming increasingly common and some organizations opt to deploy these to public app stores. The challenge with this approach is that updates to the native app container are then subject to app store turnaround times (though CodePush can help remediate concerns with static and JavaScript content).

Fortunately  , Mobile Device Management (MDM) suites like **[Microsoft Intune]**(https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) typically provide an internal app store that enable internal users to quickly gain access to both internal and externally developed apps. These features also include bring-your-own-device (BYOD) scenarios where mobile users may not want their devices controlled as strictly as corporate owned ones. 

Implementing an internal app store then allows you to reduce the amount of time it takes to make an app update available to your internal users from days to minutes. Intune's mobile app store / company portal capabilities include support for Android, iOS, Windows, and Samsung KNOX based devices.  

See **[Deploy and configure apps with Microsoft Intune](https://technet.microsoft.com/en-us/library/dn646965.aspx)** for more details.

##Additional Security Topics
- [Learn about Cordova platform and app security features](./cordova-security-platform.md)
- [Secure and encrypt your Cordova app data at rest and over the wire](./cordova-security-data.md)
- [Authenticating users with Azure Mobile Apps or the Active Directory Authentication Library for Cordova](./cordova-security-auth.md)
- [Download samples from our Cordova Samples repository](http://github.com/Microsoft/cordova-samples)
