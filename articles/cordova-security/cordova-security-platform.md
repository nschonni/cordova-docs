<properties pageTitle="Cordova platform and app security"
  description="Cordova platform and app security"
  services=""
  documentationCenter=""
  authors="clantz" />

#Cordova platform and app security
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions. Like the web, Cordova is a very open platform and as a result it does not force you down a specific path that will always garuntee a secure app. Instead provides a set of tools that you can use to lock down your app as appropriate. A forced lockdown approach can block critical scenarios and thus tends to have undesired results. For example, Windows 8.1's platform security features block the use of hosted content. This has been resolved in Windows 10 by instead providing options for locking down your app. Beyond platform features, Microsoft also has some additional options that you can use to further improve your overall app security. 

For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki). However, given the increased capabilities Cordova apps are affored, it is important to limit your risk as much as possible. This document will outline some of the security features that exist in Cordova and related Microsoft products along with some general best practices for improving the overall security of your app beyond what you may typically think about for web apps. You may also find security articles on [data encryption and transmission](./cordova-security-data.md), [authentication](cordova-security-auth.md), and [detecting / preventing / remediating issues](./cordova-security-detect.md) of interest for further information.

## Update Cordova
While always a good reccomendaiton, staying up to date with Cordova versions is a best practice as security fixes are included on a regular basis. You should absolutley not use Cordova versions < 4.3.1 as your app will be rejected from Google Play due to a [specific security issue](../tips-and-workarounds/android/security-05-26-2015.md). Further, you should do everything you can to use **Apache Cordova 5 and up** as this version provide some crticial capabilities that can improve the overall security of your app. Specifically:

1. **The Crosswalk Webview** - The Android platform in Cordova 5 and up (cordova-android 4.0.0+) introduced the ability to support "pluggable webviews" where the app itself contains a consistant webview implementation rather than the default browser on the device. Originally concieved as a way to get more consistant benhavior on Android, one implementation is the [Crosswalk Webview](https://crosswalk-project.org/) which adds an up to date version of the Chromium webview to the project. For security, this means you get access to important security features like the Content Security Policy and Web Crypto on Android as far back as Android 4.0. Further, these devices will also get WebView related security patches not present in the OS itself. 
2. **Content Security Policy (CSP) Support**: Android with Crosswalk, iOS, and Windows 10 all support adding a Content Security Policy to your app and this represents an important security tool to take advantage of for any app. A strict policy can eliminate security attack vectors at the underlying webview/browser level. 
3. **Windows 10 Support w/CSP and "Local Mode" Support:** Windows 10 features a more nuanced view of security than Windows/Phone 8.1 did with significant improvements in compatiblity between Android and iOS by default. In addition to CSP support, Windows 10 supports something called "local mode" that adds OS level security measures and allows additional native Windows 10 features to be used from within your Cordova app. These restrictions are more permissive and nuanced than those that were present in Windows 8.1 and but are off by default in Cordova apps to avoid cross-platform compatibility issues.

Read on for some additional guidence and reccomendations and see the **[Apache Cordova Security Guide](https://cordova.apache.org/docs/en/6.0.0/guide/appdev/security/index.html)** on Apache's site for additional tips.

##Use Crosswalk and Cordova 5+
As outlined above, Cordova 5 and the Crosswalk WebView can sigificantly improve the security of your app on Android particularly given the device fragmentation issues that exist today. Simply adding "cordova-plugin-crosswalk-webview" to your project when using Cordova 5+ enables these features. To add it to your project:

1. In Visual Studio, simply click "Add" on the **Crosswalk Webview Engine** plugin in the **config.xml designer.**
2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-crosswalk-webview --save
    ```

There are, however, some nuances to using the Crosswalk webveiw given it does slow down build times and requires GPU accelleration in emulators. See **[Improving Android browser consistency and features with the Crosswalk WebView](../develop-apps/cordova-crosswalk.md)** for setup details and information on the useful **Shared Mode**.

Note that Crosswalk 14 can cause a crash when using Web Crypto and Crosswalk 16 has caused crashes in certain emulators. Crosswalk 15 appears to be a solid choice. If you run into unexpected crashes or odd behaviors, simply the following config.xml to force the plugin to use version 15 (Right-Click &gt; View Code in VS):

```
<preference name="xwalkVersion" value="org.xwalk:xwalk_core_library:15+" />
```

##Use a strict content security policy
By default, simply adding a CSP declaration to an HTML page locks it down very tightly. By default, applying a CSP **disables both eval() and inline script** and only allows access to JavaScript and CSS files from the **same origin as the HTML page**. Typically for Cordova apps this means only **local content** and as a result, CDN hosted content typically cannot be referenced.  Breaking these down in terms of risk:

1. **Inline script** for JavaScript content, though extremely handy for testing and development, is one of the largest risks for attacks. When inline script is allowed, all it takes is one unescaped input pumped to innerHTML to allow a user to run any abitrary JavaScript code. Note the inline script restriction also applies to **onload** and similar HTML attributes for the exact same reason.
2. Allowing **JavaScript and CSS content from different origins** poses the next largest risk. Disabling inline script reduces the chances of attack, but even with inline script disabled you can still add a script tag with a href to an external source. The same innerHTML mistake can then lead to a similar vulnerability. While this is obviusly true for JavaScript, CSS also poses a risk because directives like expression() and url('javascript:...'). You can loosen this restriction by listing only very specific domains that you trust but exercise caution when doing so.
3. **eval()** along with related features like "new Function" also pose a risk but these are reduced if the inline and same origin restrictions are left in place. Further, eval is used by a number of JavaScript frameworks for optimization purposes so while this is the default it can become quite problematic if left in place. If your app and all associated frameworks do not make use of eval, you should disable it. Otherwise avoid using eval in your own code unless you really know exactly what you are doing.

Note that none of these rules apply to XML HTTP Requests, images, or other static content. 

The default CSP policy in Visual Studio and Cordova templates is a solid starting point. It allows eval(), but keeps the inline script and same origin restrictions in place.

```
<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://ssl.gstatic.com 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *">
```

See the **[Cordova whitelist and Content Security Policy guide](./cordova-security-whitelist.md)** for additional details on configuring a CSP policy to meet your needs.

##Manage your whitelists
When using Cordova 5+, you will need to install a whitelist plugin to enable access to external network resources on Android and iOS. Windows 10 also supports the elements included in this platform. The new whitelist plugin that is pre-installed in new Cordova projects either created from the CLI or Visual Studio actually introduce three separate elements designed to enable more discrete control that was possible in the past.

The **access** element from previous versionf of Cordova returns but only controls where your app can make XHR requests or access other external content from a web page for Android and iOS. It no longer controls whether you can navigate to a different domain (such as hosted content). A new **allow-navigation** element has been added that then enables you to specify where the app can navigate instead. Finally, a new **allow-intent** element has been introduced specifically designed to control Android intents.

The base Visual Studio [Cordova CLI](http://aka.ms/cordova-cli) template (via the cordova create command) has a config.xml file in it that is designed to allow the app to make external requests anywhere, allows a specific subset of intents, and prevents the WebView in the Cordova app to navigate anywhere other than local content.

```
<access origin="*" />
<allow-intent href="http://*/*" />
<allow-intent href="https://*/*" />
<allow-intent href="tel:*" />
<allow-intent href="sms:*" />
<allow-intent href="mailto:*" />
<allow-intent href="geo:*" />
```

This is a relatively safe starting point. To modify this list you can edit config.xml either in your favorite text editor like VS Code or by right-clicking and selecting View Source in Visual Studio. 

In general it is best to trim access down to only those URIs you actually need to use in your app and you will want to exercise great care when broadening access for your app to only include trusted sources.

Note that there are some nuances on how these whitelist work and both Windows Phone 8.0 and Windows / Windows Phone 8.1 do not support all of these elements. See the **[Cordova whitelist and Content Security Policy guide](./cordova-security-whitelist.md)** for additional details.

##When in doubt, InAppBrowser
If you must include content from an external source that you do not have complete and total control over, **use the InAppBrowser plugin** and host the content there. This plugin places content in a separate webview without access to Cordova interfaces and therefore significantly reduces the risk to your app and its data. It's easy to setup and replaces **window.open** with a secure implementaiton.

1. In Visual Studio, simply click "Add" on the **InAppBrowser** plugin in the **config.xml designer.**
2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-inappbrowser
    ```

##Use "Local Mode" for Windows 10
Windows 10 support in the Cordova Windows platform improves resolves many of the differences that existed between the Windows platform and Android and iOS.  In addition, it includes a "local mode" only allows navigation to pages hosed within the app, disables inline script, and only allows JavaScript and CSS references from within the app. The end result is a significant reduction in cross-site scripting risks since this is enforced at a platform level.

One often missed feature that the Windows platform for Cordova has is the ability to call **any** JavaScript enabled [Windows API](https://msdn.microsoft.com/en-us/library/windows/apps/br211377.aspx) from your Cordova app **without a plugin**. Many plugins for the Windows platforms are simple JavaScript adapters to conform to the plugin interface spec, so putting the app in "local mode" means that you'll usually being platform level Windows vetted APIs in a secure environment. 

Enabling it is simple. Edit config.xml either in your favorite text editor like VS Code or by right-clicking and selecting View Source in Visual Studio and add the following elements:

```
<preference name="windows-target-version" value="10.0" />
<preference name="WindowsDefaultUriPrefix" value="ms-appx://" />
```

The WindowsDefaultUriPrefix preference flips Cordova into "local mode" instead of the default "remote mode." 

See the **[Cordova Windows 10 platform documentation](http://cordova.apache.org/docs/en/latest/guide/platforms/win8/win10-support.html)** for additional details on the difference along with the additional capabilities that are enabled when submitting to the public Windows Store.

##Use the Intune App SDK
[Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) is a [mobile application managment](https://en.wikipedia.org/wiki/Mobile_application_management) (MAM) and mobile device management (MDM) platform that supports Android, iOS, and Windows devices. Intune's MAM capabilities can be used without managing devices which means it can be used in combination with existing MDM solutions like Airwatch and Mobile Iron. Currently it is targeted at Active Directory authorized apps and thus is most applicable to enterprise focused scenarios. It provides the ability to enforce policies at the **app level** including encryption of all local data, disabling cut-copy-paste, and more. A Cordova plugin for Android and iOS that is a part of Intune's App SDK enables more nuanced control that is typically availalbe from other MAM solutions. As a result it fundamentally improves the security of Cordova as an overall platform.

For Android and iOS, Intune's MAM features are enabled via an SDK that includes a cordova plugin. To install it, follow these steps:

1. In Visual Studio, right click on config.xml, select View Code, and then add one of the following to the file. The plugin will be added on next build.
    ```
    <plugin name="cordova-plugin-ms-intune" spec="~1.0.0" />
    ```
    ...or for Cordova < 5.1.1...
    ```
    <vs:plugin name="cordova-plugin-ms-intune" version="1.0.0" />
    ```

2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-ms-intune --save
    ```

**TODO: GET REAL PLUGIN ID**

See **[LINK TO DOCUMENTAITON GOES HERE]()** for more details on configuring Intune's MAM capabilities.

##Additional Security Topics
- [Secure and encrypt your Cordova app data at rest and over the wire](./cordova-security-data.md)
- [Authenticating users with Azure Mobile Apps or the Active Directory Authentication Library for Cordova](./cordova-security-auth.md)
- [Detect, prevent, and quickly remediate security issues](./cordova-security-detect.md)
- [Download samples from our Cordova Samples repository](http://github.com/Microsoft/cordova-samples)