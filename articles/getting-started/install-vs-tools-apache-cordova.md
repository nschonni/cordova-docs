<properties
   pageTitle="Install Visual Studio Tools for Apache Cordova | Cordova"
   description="description"
   services="na"
   documentationCenter=""
   authors="normesta"
   tags=""/>
<tags
   ms.service="na"
   ms.devlang="javascript"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="normesta"/>
# Install Visual Studio Tools for Apache Cordova

Use the Visual Studio Tools for Apache Cordova to build apps that run on iOS, Android, and Windows devices.

To use these tools, choose the Apache Cordova feature when you install Visual Studio. If you've installed Visual Studio already, you can always add the feature later.

You'll need a computer tht runs Windows 8, Windows 8.1, Windows 10, or Windows Server 2012 R2. You can use  Windows 7 if you want, but if you do, you won't be able to target Windows or Windows Phone devices.

## <a id="install"></a>Choose the Apache Cordova feature when you install Visual Studio

1.	In the Visual Studio installer, choose the **Custom** option, and then select the **HTML/JavaScript (Apache Cordova)** feature.

    ![Installing Visual Studio Tools for Apache Cordova](media/get-started-first-mobile-app/install-tools.png)

    Visual Studio installs all of the third-party components that you need to build your app. To see a list of these components or exclude any of them, see this [section](#choose). For most folks, this isn't necessary.

    After you’ve installed Visual Studio, go straight to our [beginner's guide](get-started-first-mobile-app.md). If have a project already, then [migrate it](migrate-to-vs2015.md).

## Add the Apache Cordova feature after you install Visual Studio
If you've already installed Visual Studio, just modify it to include the tools.

1.	Open **Control Panel** -> **Programs and Features**, choose the **Visual Studio 2015** item, and then choose the **Change** button.

    ![Modify Visual Studio Setup](media/get-started-first-mobile-app/modify-setup-2.png)

2.	In the setup wizard for Visual Studio, choose the **Modify** button.

3. In the list of optional features to install, select the **HTML/JavaScript (Apache Cordova)** checkbox, choose the **Next** button, and then choose the **Update** button.

    ![Choose Apache Corodova components](media/get-started-first-mobile-app/modify-setup.png)

    Visual Studio installs all of the third-party components that you need to build your app. To see a list of these components or exclude any of them, see this [section](#choose). For most folks, this isn't necessary.

    After you’ve installed Visual Studio, go straight to our [beginner's guide](get-started-first-mobile-app.md). If have a project already, then [migrate it](migrate-to-vs2015.md).

## <a id="choose"></a>The third-party components that Visual Studio installs for you

Visual Studio installs these third-party components. You'll need them to use Apache Cordova, and to target the Android platform.

<style>
    table, th, td {
        border: 1px solid black;
        border-collapse: collapse;
    }
    th, td {
        padding: 5px;
    }
</style>
<table>

<tbody>
    <tr>
        <th><strong>Component</strong></th>
        <th><strong>Why Visual Studio installs it**</th>
    </tr>
    <tr>
        <td><strong>Apache Ant 1.8.0</strong> (or later)</td>
        <td>To build Android apps.</td>
    </tr>
    <tr>
        <td><strong>32-bit Oracle Java JDK 7</strong></td>
        <td>To build Android apps.</td>
    </tr>
    <tr>
        <td><strong>Android SDK</strong></td>
        <td>To build Android apps and to start the Apache Ripple simulator.</td>
    </tr>
    <tr>
        <td><strong>Joyent Node.js</strong></td>
        <td>To integrate with the Apache Cordova Command Line Interface (CLI) and the Apache Ripple simulator.</td>
    </tr>
    <tr>
        <td><strong>Git CLI</strong></td>
        <td>To enable you to manually add Git URIs for specific Cordova plug-ins.</td>
    </tr>
</tbody>
</table>

You can clear the checkbox of any component that you don't want to install in the **Common Tools and Software Development Kits** group of the Visual Studio installer.

![Multi-Hybrid Device Apps installer](media/install-vs-tools-apache-cordova/IC816239.png)

If you prefer to install them manually, see [install the dependencies manually](configure-vs-tools-apache-cordova.md#ThirdParty).

## Update Visual Studio Tools for Apache Cordova

1. In Visual Studio, choose **Tools**->**Extensions and Updates**.
2. In the **Updates** tab of the **Extensions and Updates** dialog box, choose **Product Updates**.

   If an update for Visual Studio Tools for Apache appears, select it, and then choose the **Update** button.

   ![Updates dialog box](media/install-vs-tools-apache-cordova/updates.png)

## Install tools for iOS <a name="ios"></a>

If you want to build a version of your app for iOS devices, you'll need a Mac to do it. Currently, there is no way to build an iOS app without one. You can use a physical Mac or a cloud-hosted one. Just make sure that it's running Mac OS X Mavericks. Then, on your Mac, install these things.

* [Xcode](https://developer.apple.com/xcode/download/).

* The Xcode command-line tools.

    To install these, open a Terminal app on your Mac, and type this command: ```xcode-select -–install```.

* [Node.js](http://nodejs.org/).

* [Apple iTunes](http://www.apple.com/itunes/).

You'll also need these things.

* An active [Apple Developer Program](https://developer.apple.com/programs/enroll/).

* An [iOS provisioning profile](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW61) that you [download ](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW26) in Xcode.

* A signing identity that you [configure ](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html#//apple_ref/doc/uid/TP40012582-CH31-SW4) in Xcode.

### Install the remote agent

The *remote agent* is a secure build server that you can use to build, run and debug apps on your Windows PC by connecting to a Mac.

In the Terminal app, type this command:

```
  sudo npm install -g remotebuild
```

### Configure npm package cache permissions

If you have an older version of Node.js and npm, type this command:

```
sudo chown -R `whoami` ~/.npm
```

Newer versions of Node.js and npm will automatically configure the permissions of the npm package cache in your home directory.

### Start the remote agent

In the Terminal app, type this command:

  ```
  remotebuild
  ```
This starts the agent with a default build directory of *~/.taco\_home/remote-builds/taco-remote/builds*.

If you want to change this, see [Configure the remote agent](configure-vs-tools-apache-cordova.md#IosConfig).

This command also activates the developer mode on your Mac, and installs all of the software that you need to run and debug your iOS app.

One of those items is [Homebrew](http://brew.sh/) and it'll install only if you're running the Terminal app with sudo (administrator) access.

If you had to install and start the remote agent without using sudo, you'll have to install Homebrew manually. See the [Homebrew documentation](https://github.com/Homebrew/homebrew/wiki/Installation) for guidance.

### <a id="getInfo"></a>Get the information that you need to configure Visual Studio

After you start the remote agent, some information such as the host, port, and security pin appear in the Terminal app. You'll add this information to configuration settings in Visual Studio so that Visual Studio use SSL certificates to securely transfer build payloads to and from Visual Studio.

![Cordova_iOS_Install_Agent](media/install-vs-tools-apache-cordova/IC816236.png)

If you want to use the host name instead of the IP address, open a Command Prompt on your Windows computer and "ping" the Mac by using that host name.  If you're unable to reach the Mac by using that host name, consider using the IP address instead.

Sometimes a Mac is externally visible under one hostname, but internally assigns itself a different hostname. If this is the case with your Mac, you can regenerate certificates that  work with the externally visible hostname by using these commands in the Terminal app of your Mac:

```
  remotebuild certificates reset --hostname=my.external.hostname.com
  remotebuild certificates generate --hostname=my.external.hostname.com
```
> **Note** These commands aren't supported with older versions of the remote agent. If you're using an older version of the remote agent, install the remote agent again to get the most recent version.

### Configure the remote agent for use with Visual Studio

1. On the Visual Studio menu bar, choose **Tools**, **Options**.

4. In the **Options** dialog box, open **Tools for Apache Cordova**, and then choose **Remote Agent Configuration**.

4. Set the values of each field. The following table describes each field.

    <table>

     <tbody>
       <tr>
           <th><strong>Field</strong></th>
           <th><strong>Value</strong></th>
       </tr>
       <tr>
           <td><strong>Enable remote iOS processing</strong></td>
           <td><strong>True</strong></td>
       </tr>
       <tr>
           <td><strong>Secure Mode</strong></td>
           <td><strong>True</strong>
           <p>If you'd rather use simple HTTP-based connections, you can leave the <strong>Security PIN</strong> field blank and set <strong>Secure mode</strong> to <strong>False</strong>.
           <p>If you do this, you'll have to start remotebuild again to disable secure mode. In the Terminal app on your Mac, type:
           <i>remotebuild --secure false</i>.
           </p>
           </p></td>
       </tr>
       <tr>
           <td><strong>Host</strong></td>
           <td>Use the host name that you obtained in the [Get the information that you need to configure Visual Studio](#getInfo) section, or use the IP address of your Mac.</td>
       </tr>
       <tr>
           <td><strong>Port</strong></td>
           <td>Use the port that you obtained in the [Get the information that you need to configure Visual Studio](#getInfo) section.</td>
       </tr>
       <tr>
           <td><strong>Security PIN</strong></td>
           <td>Use the security pin that you obtained in the [Get the information that you need to configure Visual Studio](#getInfo) section.
           <p>
           You can use the security PIN for only one client. If you want to pair another client with the remotebuild server on your Mac, see [Generate a new security PIN](configure-vs-tools-apache-cordova.md#IosPin).
           <p></td>
       </tr>
     </tbody>
    </table>

    Your fields will look something like this:

    ![Configuring the remote agent in Visual Studio](media/install-vs-tools-apache-cordova/IC795521.png)

8. Choose the **OK** button.

   Visual Studio connects to the remote agent (the agent must be running to connect).

### Stop the remote agent

In the Terminal app on your Mac, press Ctrl+C.

#### Build and run your iOS app

When you're ready to build and run your iOS app, see any of these guides:

* [Run your Apache Cordova app on iOS](./develop-apps/run-app-ios.md).

* [Build and Simulate iOS in the Cloud](build_ios_cloud.md).

* [Build a Cordova app for iOS using Parallels](./debug-and-test/run-android-emulator-on-osx-using-parallels.md)

## Did something go wrong with your installation? <a name="AdditionalTasks">

Perhaps you're running Visual Studio behind a proxy or you have multiple versions of a third-party components installed on your computer, and you need to take a closer look at the environment variables to see what path is being used for these components.

For a more comprehensive guide to configuring Visual Studio Tools for Apache Cordova and all of the third-party dependencies, see [Configure the Visual Studio Tools for Apache Cordova](configure-vs-tools-apache-cordova.md).
