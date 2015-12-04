<properties pageTitle="Known Issues - Apache Cordova and Apache Ripple"
  description="This is an article on bower tutorial"
  services=""
  documentationCenter=""
  authors="kirupa" />
  <tags
     ms.service="na"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="mobile-multiple"
     ms.workload="na"
     ms.date="09/10/2015"
     ms.author="kirupac"/>

#**Known Issues - Apache Cordova and Apache Ripple**
This article covers general [known issues](../cordova-docs-readme.md#knownissues) related to Apache Cordova or Ripple. See [Cordova 5.x.x known issues](known-issues-cordova5.md) for details on Cordova and Ripple related issues that are specific to Cordova 5.0.0 and up.

##**Build not executing when using Cordova with Node.js 5.0.0+ and Cordova 5.3.3 and below**

If you use Cordova 5.3.3 or below with Node.js 5.0.0, you will see a build that appears to complete but does not actually execute due to an incompatibility between Cordova and this version of Node.

Cordova 5.3.3 and below does not support Node.js 5.0.0 or above. Either use Node.js 4.1.2 with Cordova 5.3.3 or Node.js 0.12.x with Cordova < 5.3.3. A planned update to Cordova (version > 5.3.3) will support Node.js 5.0.0+.

##**Build hangs or does not execute when building for iOS with Cordova < 5.3.3 and Node.js 4.0.0+**

If you use Cordova < 5.3.3 or below with Node.js 4.x.x on your Mac, you will see iOS builds hang or simply succeed without actually executing.

Cordova 5.3.3 is the first version of Cordova that supports Node.js 4.x.x. Upgrade to Cordova 5.3.3 or higher or downgrade Node.js to 0.12.x if you need to use an earlier version of Cordova.  Note that 5.3.3 does not support Node.js 5.0.0+ so you will need to use a later version if you intend to use Node.js 5.0.0 or later.

##**Plugin with variables not working**

Due to a Cordova issue with Cordova 4.3.0 and 4.3.1, you can run into problems with plugin variables in Cordova prior to 5.0.0. Plugin variable information is lost if you install the "plugin" before the "platform" which can happen depending on your workflow. The plugin variables do, however, work in Cordova 5.1.1, which you can use with VS 2015. Follow these steps to use a plugin with variables:

 1. Remove the plugin with the variables using the config designer.

 2. Update to Cordova 5.1.1 using the config designer (Platforms > Cordova CLI)

 3. Re-add your plugin using the "Plugins" tab in the config designer

##**TypeError: Request path contains unescaped characters**

When building or installing a plugin, you may encounter this error if you are using a proxy with certain versions of Node.js and Cordova after a "npm http GET". This is a Cordova issue and the simplest workaround is to downgrade Node.js to 0.10.29. This will be resolved in a future version of Cordova. See [tips and workarounds](../tips-and-workarounds/general/tips-and-workarounds-general-readme.md#cordovaproxy) for additional details.

##**Ripple - Camera plugin does not work when using DATA_URL**

When using the Camera plugin with Ripple and the DATA_URL option, a file reference is returned instead.

##**Ripple "I Haz Cheezburger?!?!" error**

This error is Ripple's way of telling you that the Cordova plugin you attempted to use is not supported in the simulator. You can, however, enter data to simulate a success or failure callback to your code from this dialog.
