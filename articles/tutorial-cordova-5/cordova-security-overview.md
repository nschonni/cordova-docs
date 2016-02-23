<properties pageTitle="An overview of Cordova security best practices"
  description="An overview of Cordova security best practices"
  services=""
  documentationCenter=""
  authors="clantz" />

# An overview of Cordova security best practices

Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions. Like the web, Cordova is a very open platform and as a result it does not force you down a specific path that will always garuntee a secure app but instead provides a set of tools that you can use to lock down your app as appropriate. A forced lockdown approach can block critical scenarios and thus tends to have undesired results. For example, Windows 8.1's platform security features block the use of hosted content. This has been resolved in Windows 10 by instead providing options for locking down your app. Microsoft also has some additional options that you can use to further improve your overall app security beyond the Cordova platform itself. 

For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki). However, given the increased capabilities Cordova apps are affored, it is important to limit your risk as much as possible. This document will outline some of the security features that exist in Cordova and related Microsoft products along with some general best practices for improving the overall security of your app beyond what you may typically think about for web apps. 

In brief, here are the general reccomendations we will cover in more detail.

1. **Cordova platform and app security:** Use up to date versions of Cordova and its plugins, use the Crosswalk plugin for Android, set a strict Content Security Policy and tightly manage your whitelists, enable "local mode" for Windows 10, use the Microsoft Intune App SDK to lock down device features when feasible.
2. **Local Storage:** Encrypt secure data using Web Crypto not a JavaScript based security implementation, avoid storing unencrypted secrets/tokens in local storage or files, use the Microsoft Intune App SDK to secure data when feasible, consider security related community plugins for secnarios where these do not fulfill your needs.
3. **Authentication/Authorization:** Use the ADAL plugin when authenticating against Active Directory, use Azure App Service for social authentication, avoid storing unencrypted secrets/tokens in local storage or files, use the Intune App SDK to force authentication via a policy when appropriate, consider community plugins where these do not fulfill your needs.
4. **Secure Data in Motion:** Always use SSL, pass your client authentication token in all calls and validate on the server, use Azure App Service to accellerate this process, consider the cordova-http plugin if certificate pinning is required.
5. **Prevent/Detect Issues:** Store remote data in a secure environment like Azure, consider products like Trend Micro Azure Deep Security, implement a MDM solution like Intune, consider Intune MAM for policy driven control at the app level along with jailbreak detection, malware dection, and more even when using another MDM solution, use a source code scanning product.
6. **Quickly Remediate:** Use CodePush to enable rapid out-of-band updates to public and entperise store environments, use a MDM solution with an enterprise store line Intune, implementat continous delivery workflow for apps and services.

##Cordova platform and app security
While always a good reccomendaiton, staying up to date with Cordova versions is a best practice as security fixes are included on a regular basis. You should absolutley not use Cordova versions < 4.3.1 as your app will be rejected from Google Play due to a [specific security issue](../tips-and-workarounds/android/security-05-26-2015.md). Further, you should do everything you can to use **Apache Cordova 5 and up** as this version provide some crticial capabilities that can improve the overall security of your app. Specifically:

1. **The Crosswalk Webview** - The Android platform in Cordova 5 and up (cordova-android 4.0.0+) introduced the ability to support "pluggable webviews" where the app itself contains a consistant webview implementation rather than the default browser on the device. Originally concieved as a way to get more consistant benhavior on Android, one implementation is the [Crosswalk Webview](https://crosswalk-project.org/) which adds an up to date version of the Chromium webview to the project. For security, this means you get access to important security features like the Content Security Policy and Web Crypto on Android as far back as Android 4.0. Further, these devices will also get WebView related security patches not present in the OS itself. 
2. **Content Security Policy (CSP) Support**: Android with Crosswalk, iOS, and Windows 10 all support adding a Content Security Policy to your app and this represents an important security tool to take advantage of for any app. A strict policy can eliminate security attack vectors at the underlying webview/browser level. 
3. **Windows 10 Support w/CSP and "Local Mode" Support:** Windows 10 features a more nuanced view of security than Windows/Phone 8.1 did with significant improvements in compatiblity between Android and iOS by default. In addition to CSP support, Windows 10 supports something called "local mode" that adds OS level security measures and allows additional native Windows 10 features to be used from within your Cordova app. These restrictions are more permissive and nuanced than those that were present in Windows 8.1 and but are off by default in Cordova apps to avoid cross-platform compatibility issues.
3. **The Intune App SDK:** [Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) has [mobile application managment](https://en.wikipedia.org/wiki/Mobile_application_management) (MAM) capabilities that enable you to enforce app level security policies even when not using Intune to manage devices. Better yet, a Cordova plugin that is a part of Intune's App SDK enables more nuanced control that is typically availalbe from other MAM solutions.

###Use Crosswalk and Cordova 5+
As outlined above, Cordova 5 and the Crosswalk WebView can sigificantly improve the security of your app on Android particularly given the device fragmentation issues that exist today. Simply adding "cordova-plugin-crosswalk-webview" to your project when using Cordova 5+ enables these features. To add it to your project:

1. In Visual Studio, simply click "Add" on the **Crosswalk Webview Engine** plugin in the **config.xml designer.**
2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-crosswalk-webview
    ```

There are, however, some nuances to using the Crosswalk webveiw given it does slow down build times and requires GPU accelleration in emulators. 

**See [Using Apache Cordova 5](./cordova-5-security.md#crosswalk)** for details.

###Use a strict content security policy
By default, simply adding a CSP declaration to an HTML page locks it down very tightly. By default, applying a CSP **disables both eval() and inline script** and only allows access to JavaScript and CSS files from the **same origin as the HTML page**. Typically for Cordova apps this means only **local content** and as a result, CDN hosted content typically cannot be referenced.  Breaking these down in terms of risk:

1. **Inline script** for JavaScript content, though extremely handy for testing and development, is one of the largest risks for attacks. When inline script is allowed, all it takes is one unescaped input pumped to innerHTML to allow a user to run any abitrary JavaScript code. Note the inline script restriction also applies to **onload** and similar HTML attributes for the exact same reason.
2. Allowing **JavaScript and CSS content from different origins** poses the next largest risk. Disabling inline script reduces the chances of attack, but even with inline script disabled you can still add a script tag with a href to an external source. The same innerHTML mistake can then lead to a similar vulnerability. While this is obviusly true for JavaScript, CSS also poses a risk because directives like expression() and url('javascript:...'). You can loosen this restriction by listing only very specific domains that you trust but exercise caution when doing so.
3. **eval()** along with related features like "new Function" also pose a risk but these are reduced if the inline and same origin restrictions are left in place. Further, eval is used by a number of JavaScript frameworks for optimization purposes so while this is the default it can become quite problematic if left in place. If your app and all associated frameworks do not make use of eval, you should disable it. Otherwise avoid using eval in your own code unless you really know exactly what you are doing.

Note that none of these rules apply to XML HTTP Requests, images, or other static content. 

The default CSP policy in Visual Studio and Cordova templates is a solid starting point. It allows eval(), but keeps the inline script and same origin restrictions in place.

```
<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://ssl.gstatic.com 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *">
```

**See [Cordova 5 Security](./cordova-5-security.md)** for additional details on configuring a CSP policy to meet your needs.

###Manage your whitelists
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

Note that there are some nuances on how these whitelist work and both Windows Phone 8.0 and Windows / Windows Phone 8.1 do not support all of these elements.  

**See [Cordova 5 Security](./cordova-5-security.md)** for additional details.

###Use "Local Mode" for Windows 10
Windows 10 support in the Cordova Windows platform improves resolves many of the differences that existed between the Windows platform and Android and iOS.  In addition, it includes a "local mode" only allows navigation to pages hosed within the app, disables inline script, and only allows JavaScript and CSS references from within the app. The end result is a significant reduction in cross-site scripting risks since this is enforced at a platform level.

Enabling it is simple. Edit config.xml either in your favorite text editor like VS Code or by right-clicking and selecting View Source in Visual Studio and add the following elements:

```
<preference name="windows-target-version" value="10.0" />
<preference name="WindowsDefaultUriPrefix" value="ms-appx://" />
```

The WindowsDefaultUriPrefix preference flips Cordova into "local mode" instead of the default "remote mode." 

**See the [Cordova Windows 10 platform documentation](http://cordova.apache.org/docs/en/latest/guide/platforms/win8/win10-support.html)** for additional details on the difference along with the additional capabilities that are enabled when submitting to the public Windows Store.

###Use the Intune App SDK
[Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) is a [mobile application managment](https://en.wikipedia.org/wiki/Mobile_application_management) (MAM) and mobile device management (MDM) platform that supports Android, iOS, and Windows devices. Intune's MAM capabilities can be used without managing devices which means it can be used in combination with existing MDM solutions like Airwatch and Mobile Iron. Currently it is targeted at Active Directory authorized apps and thus is most applicable to enterprise focused scenarios. It provides the ability to enforce policies at the **app level** including encryption of all local data, disabling cut-copy-paste, and more. A Cordova plugin for Android and iOS that is a part of Intune's App SDK enables more nuanced control that is typically availalbe from other MAM solutions. As a result it fundamentally improves the security of Cordova as an overall platform.

For Android and iOS, Intune's MAM features are enabled via an SDK that includes a cordova plugin. To install it, follow these steps:

1. In Visual Studio, simply click "Add" on the **Intune App SDK** plugin in the **config.xml designer.**
2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-ms-intune
    ```

**TODO: GET REAL PLUGIN ID**

##Local Storage
Storing data locally is relnativley straight forward with Cordova but securing it can be a bit more difficult. Note that generally using JavaScript based encryption schemes is a bad practice and [not considered secure](https://www.nccgroup.trust/us/about-us/newsroom-and-events/blog/2011/august/javascript-cryptography-considered-harmful/). Here are some recccomendations that can thankfully help for Cordova apps.

###Encrypt data using Web Crypto via Crosswalk and a shim
The best starting point whenever you are tackling a problem related to security is to rely on browser features as they undergo significant testing and have abundant real-world use going for them. Web Crypto is a W3C standard that lets the browser itself encrypt data. Historically "crypto.subtle" has [varying levels of support](http://caniuse.com/#search=web%20crypto) in browsers with one in particular being the biggest problem for Cordova: Android. Thankfully, the [Crosswalk WebView Engine plugin](https://www.npmjs.com/package/cordova-plugin-crosswalk-webview) mentioned above brings Android 4.0+ up to a recent version of Chromium including Web Crypto support. 

iOS supports crypto.subtle with a webkit prefix IE 11 and up also support web crypto but with a slightly different syntax. Thankfully, an API shim like [webcrypto-shim](https://github.com/vibornoff/webcrypto-shim) can resolve these differences for you such that the combination of Crosswalk in the shim give you native encrption capabilities.

In general, this is your best starting point. If for some reason you cannot use Crosswalk (say due to the size of the apk it creates) or are looking for more holistic solutions, there are community plugins and other solutions that can help.

###Use the Intune App SDK to force encryption
As mentioned above,[Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) is a [mobile application managment](https://en.wikipedia.org/wiki/Mobile_application_management) (MAM) and mobile device management (MDM) platform that supports Android, iOS, and Windows devices. Intune's MAM capabilities can be used without managing devices which means it can be used in combination with existing MDM solutions like Airwatch and Mobile Iron. Currently it is targeted at Active Directory authorized apps and thus is most applicable to enterprise focused scenarios. It provides the ability to enforce policies at the **app level** including encryption of all local data. It's therefore a low friction way to increase your security. See above for details on installing the plugin.

###Consider community plugins
In addition to the above base capabilities there are a number of community plugins that can be used to encrypt data locally. Microsoft does not directly support these plugins, so security focused organizations should be sure to run a static and or dynamic code analysis tool on the resulting project code (including these plugins) during any planned security audits.

<style>
    table, th, td {
        border: 1px solid black;
        border-collapse: collapse;
    }
    th, td {
        padding: 5px;
    }
</style>
<table><thead>
<tr>
<td align="left"><strong>Topic</strong></td>
<td align="left"><strong>Plugin</strong></td>
<td align="left"><strong>Purpose</strong></td>
<td align="left"><strong>Platform Support</strong></td>
</tr>
</thead><tbody>
<tr>
<td align="left">Token / Secret Storage</td>
<td align="left"><strong><a href="https://www.npmjs.com/package/cordova-plugin-secure-storage">cordova-plugin-secure-storage</a></strong></td>
<td align="left">The plugin allows your application to securely store secrets such as auth tokens in native secure key/token stores.</td>
<td align="left">Android, iOS</td>
</tr>
<tr>
<td align="left">File Encryption</td>
<td align="left"><strong><a href="https://www.npmjs.com/package/cordova-safe">cordova-safe</a></strong></td>
<td align="left">This plugin provides a streamlined interface for encrypting files using underlying native APIs.</td>
<td align="left">Android, iOS</td>
</tr>
<tr>
<td align="left">Encrypted Database</td>
<td align="left"><strong><a href="https://github.com/litehelpers/Cordova-sqlcipher-adapter">cordova-sqlite-storage</a></strong> + Web Crypto</td>
<td align="left">WebSQL is available on iOS and Android for storing data and can be combined with Web Crypto to store encrypted values in a the database. This plugin uses the same API to store data in a SQLite database without storage limits among other features.</td>
<td align="left">Android, iOS, Windows / Phone 8.1, Windows 10 (??? - no Win 10 today)</td>
</tr>
<tr>
<td align="left">Encrypted Database</td>
<td align="left"><strong><a href="https://github.com/litehelpers/Cordova-sqlcipher-adapter">cordova-sqlcyper-adapter</a></strong></td>
<td align="left">An in-development enhanced adapter on top of the cordova-sqlite-storage plugin that uses SQLCipher to encrypt all data stored in a local database.</td>
<td align="left">Android, iOS, Windows / Phone 8.1, Windows 10 (??? - no Win 10 today)</td>
</tr>
</tbody></table>

##Authentication/Authorization
A suprisingly hard yet critical task for application security is authenticating and authorizing users to access your app and any secured local or remote data. We'll cover two Microsoft solutions and mention a few 3rd party options.

###Azure App Service Auth and Azure Mobile Apps
[Azure App Service](https://azure.microsoft.com/en-us/services/app-service/) is a suite of services designed to help you build great web and mobile apps. Cordova can directly benifit from these same features and also has the added benifit of being JavaScript based and therefore can easily take advantage of the JSON based service APIs that are being introduced even when a client library is not directly available. [Azure Mobile Apps](https://azure.microsoft.com/en-us/services/app-service/mobile/) are mobile integrated client apps that take advantage of features within the broader Azure App Service.

A core first step in accessing all of these great services, however, is authorizing users both to access the app for the app to then access data in the cloud. Fortunatley, the Cordova plugin for Azure Mobile Apps has an authentication interface currently supports authenticating against Azure Active Directory, Facebook, Google, Twitter, and Microsoft accounts. You can also expect additional features and additional provider options in the future.

You can take advantage of these  features by installing the Azure Mobile Apps Cordova plugin.

1. In Visual Studio, simply click "Add" on the **Azure Mobile Apps** plugin in the **config.xml designer.**
2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-plugin-ms-azure-mobile-apps
    ```

From here you can lock down data or other services you have in Azure using the .NET or Node.js server SDKs. All of the Azure App Service uses Azure Authorization on the server which means you'll be able to quickly connect to custom [App Service "API Apps" as well](https://azure.microsoft.com/en-us/documentation/articles/app-service-api-authentication/).

See the **[Azure Mobile Apps authentication documentation](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-cordova-get-started-users/)** for additional details on setup.


###Active Directory Authentication Library for Cordova

###JavaScript & 3rd Party Options
ng-cordova-auth - be sure to securely store auth tokens

3rd parties like Auth0 provide cordova plugins

##Secure Data in Motion

SSL good. Use cordova-http plugin for cert pinning given not available in webview?

Pass auth tokens, not user name pwd. Azure App Service can simplify for you.

##Prevent/Detect Issues

1. Use a source code scanning solution - provide examples (HP Fortify, SonarQube)
2. Intune for MAM/MDM
3. Server side threat detection - Azure products like Trend Micro Azure Deep Security
4. Threat detection? (Eg Adallom for custom apps, Lookout technologies)
5. Governance? (eg Adallom for custom apps)

##Quickly Remediate

###CodePush

###Intune MDM

## More Information
* [Read more about Apache Cordova 5](./tutorial-cordova-5-readme.md)
* [Read tutorials and learn about tips, tricks, and known issues](./cordova-docs-readme.md)
* [Download samples from our Cordova Samples repository](http://github.com/Microsoft/cordova-samples)
