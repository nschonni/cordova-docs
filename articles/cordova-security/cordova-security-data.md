<properties pageTitle="Securing Cordova app data using encryption, SSL, Intune, and Azure Mobile Apps"
  description="Secure Cordova app data using encryption, SSL, certificate pinning. Use the Intune MAM App SDK, Azure Mobile Apps, and the Azure App Service to speed up your workflow."
  services=""
  documentationCenter=""
  authors="clantz" />

# Secure and encrypt your Cordova app data at rest and over the wire
Security is a very broad topic that covers a number of different aspects of an app's lifecycle. Securing an app often represents a number of tradeoffs and key decisions. Like the web, Cordova is a very open platform and as a result it does not force you down a specific path that will always garuntee a secure app. Instead provides a set of tools that you can use to lock down your app as appropriate. 

For the most part you should apply the same [best practices to your code as you do for web apps](https://code.google.com/archive/p/browsersec/wikis/Main.wiki). However, given the increased capabilities Cordova apps are affored, it is important to limit your risk as much as possible. This document will outline some of the security features that exist in Cordova, community plugins, and related Microsoft products for encrypting data locally and transmitting it securely. 

## Adding plugins mentioned in this article
A number of the plugins in this article are not in the Visual Studio config.xml designer. You can add these plugins as follows:

1. In Visual Studio, right click on config.xml, select View Code, and then add one of the following depending on whether or not a Git URI needs to be used. The plugin will be added on next build.
    ```
    <plugin name="cordova-sqlite-storage" spec="~0.8.2" />
    <plugin name="io.litehelpers.cordova.sqlcipher" src="https://github.com/litehelpers/Cordova-sqlcipher-adapter.git" version="0.1.4-rc" />
    ```
    ...or for Cordova < 5.1.1...
    ```
    <vs:plugin name="cordova-sqlite-storage" version="0.8.2" />
    <vs:plugin name="io.litehelpers.cordova.sqlcipher" src="https://github.com/litehelpers/Cordova-sqlcipher-adapter.git" version="0.1.4-rc" />
    ```

2. When using the command line or Visual Studio Code, you can add the plugin using the Cordova CLI as follows:

    ```
    cordova plugin add cordova-sqlite-storage --save
    cordova plugin add https://github.com/litehelpers/Cordova-sqlcipher-adapter.git --save
    ```

##Secure locally stored data
Storing data locally is relativley straight forward with Cordova but securing it can be a bit more difficult. Generally using JavaScript based encryption schemes is a bad practice and [not considered secure](https://www.nccgroup.trust/us/about-us/newsroom-and-events/blog/2011/august/javascript-cryptography-considered-harmful/) and local and file storage are not encrypted. Further, features like [Apple's much discussed data protection](https://support.apple.com/en-us/HT202064) features are enabled by simply setting a PIN (which products like Intune can force to happen for your app), and you may have multi-tenet requirements where data separation is required when multiple users access the same device.

Here are some recccomendations that can thankfully help encrypt sensative data in Cordova apps. 

###Encrypt data using Web Crypto via Crosswalk and a shim
The best starting point whenever you are tackling a problem related to security is to rely on browser features as they undergo significant testing and have abundant real-world use going for them. Web Crypto is a W3C standard that lets the browser itself encrypt data. Historically [crypto.subtle.encrypt and decrypt](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto) has [varying levels of support](http://caniuse.com/#search=web%20crypto) in browsers with one in particular being the biggest problem for Cordova: Android. 

#### Crosswalk
Thankfully, the [Crosswalk WebView Engine plugin](https://www.npmjs.com/package/cordova-plugin-crosswalk-webview) brings Android 4.0+ up to a recent version of Chromium including Web Crypto support. See **[Improving Android browser consistency and features with the Crosswalk WebView](../develop-apps/cordova-crosswalk.md)** for additional details on setup including some important information on **emulator config**. 

Note that Crosswalk 14 can cause a crash when using Web Crypto and Crosswalk 16 has caused crashes in certain emulators. Crosswalk 15 appears to be a solid choice. If you run into unexpected crashes or odd behaviors, add this to config.xml (Right-Click &gt; View Code in VS):

```
<preference name="xwalkVersion" value="org.xwalk:xwalk_core_library:15+" />
```

#### webcrypto-shim
iOS supports crypto.subtle with a webkit prefix IE 11 and up also support web crypto but with a slightly different syntax. Thankfully, an API shim like [webcrypto-shim](https://github.com/vibornoff/webcrypto-shim) can resolve these differences for you such that the combination of Crosswalk in the shim give you native encrption capabilities.

In general, this is your best starting point. If for some reason you cannot use Crosswalk or are looking for more holistic solutions, there are community plugins and other solutions that can help.  

#### Example
Here is a small code sample that demonstrates using the API to encrypt a string on Android, iOS, and Windows after adding the **[cordova-plugin-crosswalk-webview](https://www.npmjs.com/package/cordova-plugin-crosswalk-webview)** plugin to your project and referencing **[webcrypto-shim.js](https://github.com/vibornoff/webcrypto-shim)** and **[promiz.js](https://github.com/Zolmeister/promiz)** in your HTML:

```javascript
var stringToEncrypt = "Hey! Encrypt me!";

// Setup AES-CBC encryption
var randomVals = new Uint8Array(16);
crypto.getRandomValues(randomVals);
var cryptoSubtleAlgo = { "name": "AES-CBC", "length": 256, "iv": randomVals };

// Generate a key, encrypt
crypto.subtle.generateKey(cryptoSubtleAlgo, true, ["encrypt", "decrypt"])
    .then(function (key) {
        return crypto.subtle.encrypt(cryptoSubtleAlgo, key, stringToArrayBuffer(stringToEncrypt))
    })
    .then(function (result) {
        // Log encrypted string
        console.log("Before: " + stringToEncrypt);
        console.log("After: " + arrayBufferToString(result));
    });


// These two functions are from a great type conversion tip at 
// https://developers.google.com/web/updates/2012/06/How-to-convert-ArrayBuffer-to-and-from-String

function stringToArrayBuffer(str) {
    var buf = new ArrayBuffer(str.length*2); // 2 bytes for each char
    var bufView = new Uint16Array(buf);
    for (var i=0, strLen=str.length; i < strLen; i++) {
        bufView[i] = str.charCodeAt(i);
    }
    return buf;
};

function arrayBufferToString(buf) {
    return String.fromCharCode.apply(null, new Uint16Array(buf));
};

```

###Use the Intune App SDK to force encryption
As mentioned above,[Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) is a [mobile application managment](https://en.wikipedia.org/wiki/Mobile_application_management) (MAM) and mobile device management (MDM) platform that supports Android, iOS, and Windows devices. Intune's MAM capabilities can be used without managing devices which means it can be used in combination with existing MDM solutions like Airwatch and Mobile Iron. Currently it is targeted at Active Directory authorized apps and thus is most applicable to enterprise focused scenarios. It provides the ability to enforce policies at the **app level** including encryption of all local data. It's therefore a low friction way to increase your security. 

The Intune App SDK has multi-tenet encryption and access capabilities that allow you to go beyond OS level data protection and allows you to ensure data separation when multiple users access the same device. For Android and iOS, Intune's MAM features are enabled via an SDK that includes a cordova plugin. 

Simply add **[cordova-plugin-ms-intune]()** to your project as described above to get started. **TODO: GET REAL PLUGIN ID**

See **[LINK TO DOCUMENTAITON GOES HERE]()** for more details on configuring Intune's MAM capabilities.

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
<td align="left"><p>The plugin allows your application to securely store secrets such as auth tokens or encryption keys in native secure key/token stores.</p>
<p>Note that a side effect of this plugin (which could be good or bad depending on the situation) is it forces users to set a PIN on Android devices since this is required to enable the generation of cryptographic keys.</p>
</td>
<td align="left">Android, iOS</td>
</tr>
<tr>
<td align="left">Encrypted Database</td>
<td align="left"><strong><a href="https://github.com/litehelpers/cordova-sqllite-storage">cordova-sqlite-storage</a></strong> + Web Crypto</td>
<td align="left"><p>WebSQL is available on iOS and Android for storing data and can be combined with Web Crypto to store encrypted values in a the database. However, WebSQL is limited to 50mb on iOS. There are a set of plugins that use the same API to store data in a SQLite database without storage limits among other features. The edition of this plugin you select will depend on your needs: </p>
<ul>
<li><a href="https://github.com/litehelpers/cordova-sqlite-storage">cordova-sqlite-storage</a> - Base version of the SQLite storage plugin with Android and iOS support.</li>
<li><a href="https://github.com/litehelpers/cordova-sqlite-ext">cordova-sqlite-ext</a> - SQLite plugin with added Windows 8.1 support (no Windows 10 yet).</li>
<li><a href="https://github.com/litehelpers/cordova-sqlite-enterprise-free">cordova-sqlite-evfree</a> - An enhanced version of the SQLite plugin targeted at enterprises with additional features and the ability to step into a support contract. You will need to install this version of the plugin using the **Git URI**: https://github.com/litehelpers/cordova-sqlite-enterprise-free.git</li>
<li><a href="https://github.com/litehelpers/cordova-sqlite-evfree-ext">cordova-sqlite-evfree-ext</a> - cordova-sqlite-evfree with added Windows 8.1 support (no Windows 10 yet). You will need to install this version of the plugin using the **Git URI**: https://github.com/litehelpers/cordova-sqlite-enterprise-free.git</li>
<ul>
</td>
<td align="left">Android, iOS, Windows 8.1 (-ext versions)</td>
</tr>
<tr>
<td align="left">Encrypted Database</td>
<td align="left"><strong><a href="https://github.com/litehelpers/cordova-sqlcipher-adapter">cordova-sqlcipher-adapter</a></strong></td>
<td align="left"<p>An in-development enhanced adapter on top of the cordova-sqlite-storage plugin that uses SQLCipher to encrypt all data stored in a local database. You will need to install this version of the plugin using the **Git URI**: https://github.com/litehelpers/cordova-sqlcipher-adapter.git</p></td>
<td align="left">Android, iOS</td>
</tr>
</tbody></table>

###Consider native Windows APIs for Windows
As mentioned above, one often missed feature that the Windows platform for Cordova has is the ability to call **any** JavaScript enabled [Windows API](https://msdn.microsoft.com/en-us/library/windows/apps/br211377.aspx) from your Cordova app **without a plugin**. Many plugins for the Windows platforms are simple JavaScript adapters to conform to the plugin interface spec. 

This includes all features contained within the **Windows.Security** and **Windows.Security.Cryptography** namespaces! However, be aware that there may be some variations between Windows 10 and Windows 8.1 APIs depending on which OS you are targeting.

 See **[Windows API documentation](https://msdn.microsoft.com/en-us/library/windows/apps/br211377.aspx)** for additional details and specifics.
 
##Secure data over the wire
###SSL and Auth Tokens
General web best practices apply to Cordova based development including an obvious but sometimes skipped reccomendation: **Always use SSL**. While this seems obvious for calls you make that contained sensative data, it is also important for **any service call that is authenticated** since you will need to pass authentication information like access tokens across in your calls. This is even more important for Cordova since native authentication [bearer tokens](http://self-issued.info/docs/draft-ietf-oauth-v2-bearer-19.html) often last longer than web based ones. In addition, libraries like ADAL support "auto-refresh" of these tokens as user expecations are that you typically log in once for a given app when you first start it up and then very infrequantly thereafter and in some cases the authorization information can be sourced from the OS itself for complete single sign on experience across apps. These differences partly come from the fact that the assumption is that you are presisting auth tokens in a secure way when using native API (or via library like ADAL that does it for you) generally not available to the web.

A second related reccomendation is to authenticate and authorize all calls using a user login driven authentication token rather than user name and password or an app-level token. The challenges with user name and password are obvious as the information must be passed in clear text. App level authentication may be acceptible in low security scenarios, but typically you will not want to rely on this approach as changing the app authentication will require an app update to accomplish. [Azure Key Valult](https://azure.microsoft.com/en-us/services/key-vault/) can help with situations where you must use an app or service level authentication token / secret / certificate by hiding these values behind a service that is itself authenticated. That said, in general it is best to keep these types of service calls behind an app specific service layer rather than having an app call them directly.

####Azure App Service
Mobile Backend as a Service (MBaaS) solutions can help you get up and running quickly with an authenticated service that can resovle the above challenges. [Azure App Service](https://azure.microsoft.com/en-us/services/app-service/) is specifically designed for this purpose and you can integrate with it easily using the [Azure Mobile Apps](https://azure.microsoft.com/en-us/services/app-service/mobile/) Cordova plugin. See [Azure App Service Auth and Azure Mobile Apps](./cordova-security-auth.md) for information on adding it to your app. 

Note that if you would prefer to use the ADAL plugin to authenticate users in your app, you can still pass the token you get from ADAL into the Mobile Apps client for interacting with the server.

```javascript
var client = WindowsAzure.MobileServicesClient(appUrl);

client.login("aad", {"access_token": tokenFromADAL})
    .then(function () {
        // Do something with the client!
     }, handleError);
```

**TODO - Verify is access_token not authenticationToken or something else**

On the server, you can also create create your own custom .NET, Java, or Node.js based services that use [Azure App Service Auth](https://azure.microsoft.com/en-us/documentation/articles/app-service-api-authentication/). Even if you opt not to use the Mobile Apps client, you simply need to pass the appropriate auth token into the request header when making a web service call. 

See the [Authenticating users with Azure Mobile Apps or the Active Directory Authentication Library for Cordova](./cordova-security-auth.md), [Azure Mobile Apps](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/), and [Azure App Service Auth](https://azure.microsoft.com/en-us/documentation/articles/app-service-api-authentication/) documentation for additional details. 

#### Calling REST APIs directly
Even if you are not using Azure Mobile Apps, Cordova's JavaScript based approach makes calling JSON based REST services easy. The [Azure Active Directory Quick Start](https://azure.microsoft.com/en-us/documentation/articles/active-directory-devquickstarts-cordova/) has code that demonstrates calling the Azure AD Graph REST API directly using an AD token from the ADAL Cordova plugin as the auth [bearer token](http://self-issued.info/docs/draft-ietf-oauth-v2-bearer-19.html). Here's a simplified example:

```javascript
function get10UsersFromADGraph(adTenantId, accessTokenFromADAL, callback) {
    var req = new XMLHttpRequest();
    req.open("GET", "https://graph.windows.net/" + adTenantId + "/users?api-version=2013-11-08&$top=10", true);
    
    // Pass in the ADAL token in the request 
    req.setRequestHeader('Authorization', 'Bearer ' + accessTokenFromADAL);

    req.onload = function(e) {
        if (e.target.status >= 200 && e.target.status < 300) {
            // Call callback function with resulting JSON from API
            callback(JSON.parse(e.target.response));
            return;
        } else {
            console.error("Call failed: " + e.target.response);        
        }    
    };
    req.onerror = function(e) {
        console.error("Call failed: " + e.error);
    };

    req.send();
}
```

This general approach can be reused across Azure services and O365 services. See documentation on [Azure JSON based REST APIs](https://msdn.microsoft.com/en-us/library/azure/hh974476.aspx) and [O365](http://dev.office.com/getting-started/office365apis) for additional details on token passsing to down-stream services. 

###Certificate Pinning
Another trick used in high secirty situations is something called [certificate pinning](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning). The idea here is you can significantly reduce the chances of a [man-in-the-middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) by "pinning" the allowed public certificates accepted by your app when making the connection to highly trusted, offical certificate authorities (like Verisign, Geotrust, GoDaddy) that you are actually using - typically only one. The end result is that someone trying to execute a man in the middle attack would need a valid SSL certificate from that specific authority to trick your app into connecting to it.

Cordova and most underlying native webviews unfortunatley do not generally support this out-of-box. You can technically approximate certificate pinning as described in the [Cordova Security Guide](https://cordova.apache.org/docs/en/6.0.0/guide/appdev/security/index.html), but the Telerik Verified **[cordova-plugin-http](https://github.com/wymsee/cordova-HTTP)** community plugin is designed to provide an API compatible XML HTTP Request implementation that adds support for certificate pinning among othere features to **iOS and Android**. In general it is best to stick with the base XML HTTP Request implementation when making service calls but this plugin can be useful when you are in a particularly high security situation. 

First, get the certificate you want to pin. It should be a DER formatted .cer file.  See [cordova-plugin-http](https://github.com/wymsee/cordova-HTTP) docs for details. Next, place the .cer file either:
1. The res/native/android/assets and res/native/ios folders when using VS (or after installing the cordova-plugin-vs-taco-support plugin for CLIs)
2. The root of your www folder (a bit less secure).

Next, add [cordova-plugin-http](https://www.npmjs.com/package/cordova-plugin-http) to your project as described above and add the following to your "deviceready" event handler:

```javascript
cordovaHTTP.enableSSLPinning(true, 
    function () {  console.log("Cert pinning enabled!"); }, 
    function () {  console.error("Cert pinning setup failed!"); });
```

You can now easily make web service calls the following to make calls that require a pinned certificate:

```javascript
cordovaHTTP.get("https://mysecuresite.com/", {}, {}, function (response) {
    console.log(JSON.stringify(response));
}, function (response) {
    console.error(JSON.stringify(response));
});
```

Finally, avoid self-signed certificates like the plague. They make you vulnerable to these man-in-the-middle attacks and are not supported out-of-box by Cordova. There are plugins that enable this particular feature, but we strongly reccomend against using them if at all possible.

###Resource Access Controls via MDM
When building an internal facing app, Mobile Device Management (MDM) and Mobile Application Managment (MAM) solutions like [Microsoft Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) can help you restrict access to services and network resources by enforcing data access controls for enrolled devices. Features include:

- Allowing you to require VPN or secure Wifi access to connect to key services by helping you [manage device profiles](https://technet.microsoft.com/en-us/library/dn997277.aspx)
- [Blocking apps from running](https://technet.microsoft.com/en-us/library/mt627829.aspx) on rooted or jailbroken devices 

If you have apps that can access particularly sensative internal data, you will want to consider using a solution line [Intune](https://www.microsoft.com/en-us/server-cloud/products/microsoft-intune/) or Airwatch to manage your devices.

Note that the Intune App SDK mentioned previously can also force authentication at an app level even if the app itself does not require authentication as a part of Intune's Mobile Application Managment (MAM) features. This allows administrators to add an additional validation in place before entering an app that may be accessing data.  In addition, products like [Azure Rights Management](https://products.office.com/en-us/business/microsoft-azure-rights-management) and [Adallom](https://www.adallom.com/) particularly when coupled with [Azure AD Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection/) can also be used to ensure that only authorized users can access sensative data.  See [Prevent, detect, and remediate security issues](./cordova-security-detect.md) for more details.

##Additional Security Topics
- [Learn about Cordova platform and app security features](./cordova-security-platform.md)
- [Authenticating users with Azure Mobile Apps or the Active Directory Authentication Library for Cordova](./cordova-security-auth.md)
- [Detect, prevent, and quickly remediate security issues](./cordova-security-detect.md)
- [Download samples from our Cordova Samples repository](http://github.com/Microsoft/cordova-samples)
