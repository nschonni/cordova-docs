<properties pageTitle="Release Notes for Update 8"
  description="Release notes for Update 8 of Visual Studio 2015 Tools for Apache Cordova"
  services=""
  documentationCenter=""
  authors="rido-min" />
  <tags
     ms.service="na"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="mobile-multiple"
     ms.workload="na"
     ms.date="03/08/2016"
     ms.author="rmpablos"/>

#**Update 8 - Visual Studio Tools for Apache Cordova**
Update 8 corresponds to Visual Studio Tools for Apache Cordova version number 14.XXXX.X 

## Setup Instructions
The most common way to get this update will be the Notification Icon in VS and the Tools & Extensions Updates,
however from this update we are offering also a standalone installer that you can find here:

[Visual Studio Tools for Apache Cordova Update 8 Download](http://go.microsoft.com/fwlink/?LinkId=XXXX)

Please note that this installer will require you to have already installed a previously version of VS TACO.

## New Features:

### NPM Sandboxing
We have seen different problems related to the version of node that is installed on your machine to isolate from these issues we have decided to ship a known version of node, sandboxed with VS installation.

### NPM Proxy
If you are behind a proxy you will have npm related problems. Based on user feedback we are detecting if there is a proxy configured at system level, and we apply the same configuration to npm.

### Updated Plugins List
Visual Studio offers a list of most common used plugins. We are replacing the `com.microsoft.azure-mobile-services` with the new `cordova-plugin-ms-azure-mobile-apps`. 
We also added more popular plugins:
- `cordova-plugin-ms-azure-mobile-apps`
- `cordova-plugin-hockeyapp`
- `cordova-plugin-code-push`
- `cordova-plugin-bluetoothle`
- `phonegap-plugin-push`
- `cordova-plugin-ms-azure-mobile-engagement`

## Bugs fixed in this release

### Associate with store (Windows) crash Visual Studio
There was a known issue that makes Visual Studio crash when users select the "associate to store" feature. We have fixed as part of Visual Studio Update 2.

 
