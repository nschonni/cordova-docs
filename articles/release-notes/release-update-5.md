<properties pageTitle="Release Notes for Update 5"
  description="Release notes for Update 5 of Visual Studio 2015 Tools for Apache Cordova"
  services=""
  documentationCenter=""
  authors="Linda" />
  <tags
     ms.service="na"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="mobile-multiple"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="lizhong"/>

#**Update 5 - Visual Studio Tools for Apache Cordova**
This document covers what's new within this update.
Version Number: 14.0.51123.1

## Setup Instructions
The most common way to get this update will be the Notification Icon in VS and the Tools & Extensions Updates,
however from this update we are offering also a standalone installer that you can find here:

[Download Update 5 Link](http://go.microsoft.com/fwlink/?LinkId=715516) 

Please note that this installer will require you to have already installed a previously version of VS TACO.

##New Features:

The main focus of this release is to improve our CLI interop story, this means that all the operations you do from the command line will be respected by the IDE and in the same way the operations you perform using the IDE will be recognized by CLI tools.

To be able to implement this interoperability story we added the next features

###Find installed plugins from plugins folder and config.xml

Previously, we were storing plugins added with VS using the '&lt;vs:plugin /&gt;' syntax in the config.xml file, now we are using the default  '&lt;plugin /&gt;' element so other command line tools know how to look for plugins.

In the case where the plugin have been added without saving it to the config.xml file, we will read the plugins folder and mark those as installed.

###Use the global Cordova version
You can specify the version of cordova you want to use from the platform section in the Config.Xml, 
and now you can use the global installed version. (This will include the proper Node version)
This feature will allow a better integration with CLI workflows, specially in environments where there is no Visual Studio IDE like Mac OS and Linux.

###Use MSBuild to build from command line
In case you want to integrate cordova projects with existing solutions and build systems based on msbuild, we have reviewed the MSBuild properties and set default values. So know you can build cordova project from command line without the need to specify additional properties. This will help to build cordova solutions using TFS 2013 or any other Build Server.

###iOS build updates
We added support for iOS 6s simulator, and improved the incremental build feature. (You will need to update the [remotebuild tools](http://taco.tools) to version 1.0.2 or greater.

##Bugs Solved
 
###Deploy to Ripple should error if VS is running as admin
We fixed a bug that did not allow to run the Ripple emulator if you started VS with admin privileges.
 
###Dev14_RTM feed does not set correct Android SDK 23 regkey
Expanding support for Android SDK 23
 
###System JAVA_HOME not respected by Visual Studio
There are some scenarios where a custom Java SDK is needed, like using a x64 version of it. Now the JAVA_HOME variable is used by VS.
 
###Plugins install errors cause inconsistencies in Plugins window

 
###MySQL breaks cordova
 
###Better error messages for iOS remotebuilds 
 

##Known Issues

**Node.JS 5.0 build fail**

There are known issues with Cordova and the latest versions of Node.JS. For example, using Cordova 5.3.3 or below with Node.js 5.0.0 causes a build fail. 

To learn more about what versions of Cordova are compatible with Node.JS, find [more information here.](http://taco.visualstudio.com/en-us/docs/known-issues-general/#strongbuild-not-executing-when-using-cordova-with-nodejs-500-and-cordova-533-and-belowstrong)



**Mismatched plugins warning**

A warning banner pops up on Visual Studio when users add the latest version of plugins to a previous version of Cordova. We recommend you always update to the lastest version of Cordova to reduce the risk of build errors. 
