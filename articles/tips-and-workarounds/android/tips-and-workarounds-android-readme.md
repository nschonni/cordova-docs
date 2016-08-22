<properties pageTitle="Android tips and workarounds"
  description="This is an article on bower tutorial"
  services=""
  documentationCenter=""
  authors="kirupa" />
  <tags ms.technology="cordova" ms.prod="visual-studio-dev14"
     ms.service="na"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="mobile-multiple"
     ms.workload="na"
     ms.date="08/21/2016"
     ms.author="mikejo"/>

#Android tips and workarounds
This document covers tips, tricks, and known workarounds for problems with the Cordova Android platform.
> **Note**: If your problem is security related, please read [May 26th, 2015 Android Security Issue](../tutorial-cordova-5-readme/)

<a name="android"></a>
##Resolve Android build and deployment errors

Try these steps if you have trouble building and deploying to Android emulators or devices. In some cases, you may see an error about a failure to install the APK on the device or a failure to run the Android Debug Bridge (ADB.exe).

>**Note**: For instructions to run for the first time on specific emulators or an Android device, see [Run your app on Android](../develop-apps/run-app-apache.md).

1. Before taking any other steps, try running your app against other platforms or emulators to make sure the issue is Android-specific.

    For example, we suggest you test on Windows:
    * Open the configuration designer (config.xml), the **Windows** tab, select your OS version, and then try running against **Windows-x64** or **Windows-x86** and select **Local Machine**.

    If you see the same error on other platforms, the issue is likely not Android-specific, see [Resolve build and deployment errors](../general/tips-and-workarounds-general-readme.md) for more general help.

2. If you are running on an Android device, make sure that **Developer Mode** is enabled on the device. Try the instructions [here](http://www.greenbot.com/article/2457986/how-to-enable-developer-options-on-your-android-phone-or-tablet.html) to enable developer mode.

    After you enable developer mode, go to *Developer options* on the device and enable Android debugging (USB Debugging).
    In USB settings, also make sure you are connecting as a *Media device*.
2. If you are running on a device and the app is already installed, uninstall the app and try again.
4. Verify that the Android SDK can connect to your device or emulator. To do this, take these steps.

    * Start an emulator or connect a device.
    * Open a command line and go to the folder where abd.exe is installed. For example, this might be C:\Program Files (x86)\Android\android-sdk\platform-tools\adb.exe
    * Type the command `adb devices` and you should see a connected device.
    ![See the connected devices](media/tips-and-workarounds-android-readme/adb-devices.png)

    If you don't see the device, restart your device or emulator and use a different USB port. (Sometimes, front USB ports are low power ports. Avoid using a USB extension cable.) Recheck using `adb devices`.

    For additional ADB commands, see [this article](http://www.androidcentral.com/android-201-10-basic-terminal-commands-you-should-know).

5. Make sure that you have the [required SDK components installed](https://taco.visualstudio.com/en-us/docs/configure-vs-tools-apache-cordova/#ThirdParty).
6. If there appears to be a problem with the Android SDK, you may need to re-install it. Before re-installing, delete the /User/username/.android and the /User/username/.gradle folder to make sure you get a fresh copy of the SDK. After [installing the SDK](http://go.microsoft.com/fwlink/?LinkID=396873), try again.

<a name="haxm"></a>
##Resolve issues with the HAXM driver

The HAXM driver is used to improve the performance of the Google Android Emulator. Conflicts with other technology that uses virtualization, such as Hyper-V, Avast, and Windows 10 Device Guard may prevent the HAXM driver from installing or working correctly. If you want to install the HAXM driver, see [this article](http://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM).

The issue may appear as an HAXM installation error or as an error indicating that you need to enable VT-x in the BIOS.

To fix the issue:

1. Disable Hyper-V (go to Control Panel > Programs, look under Programs and Features, and click **Turn Windows features on or off**).

    If Hyper-V is enabled, disable it, reboot, and retry HAXM.
2. Enable VT-x in the BIOS.

    If VT-x is disabled, enable it, reboot, and retry HAXM.
3. Check whether you have some antivirus software (like Avast) or other software using hardware-assisted virtualization and disable or uninstall the software. Reboot and retry HAXM.

    For Avast, first try to disable it by going to Avast settings and deselecting (unchecking) these options:
      * **Enable hardware-assisted virtualization**
      * **Enable avast self-defense module**

    Then reboot and try again.
4. If HAXM is not working at this point, try the following steps in order:
    * Reboot and disable VT-x in the BIOS.
    * Reboot and enable VT-x in the BIOS.
    * Uninstall HAXM and re-install it.


<a name="couldnotcreatevm"></a>
##Could not create Java Virtual Machine error
When building for Android, you may encounter a set of errors in the **Errors List** like the following:

```
Error		Could not create the Java Virtual Machine.			
Error		A fatal exception has occurred. Program will exit.									
Error		C:\cordova\BlankCordovaApp2\BlankCordovaApp2\platforms\android\cordova\build.bat: Command failed with exit code 1
```

The problem is that the Ant or Gradle build systems are running out of heap memory when you try to compile your app. To resolve this problem, you can increase the heap of the JVM by setting the following environment variable and restarting Visual Studio:

```
_JAVA_OPTIONS=-Xmx512M
```

More specifically, following the instructions from [this article](http://www.tomsguide.com/faq/id-1761312/fix-create-java-virtual-machine-issue.html):

1. Close Visual Studio, if you do not close it - you will need to restart it at the end.
2. Open the **Control Panel**.
3. Go to **System and Security**.
4. Go to **System**.
5. Go to **Advanced** systems settings.
6. Go to **Environment Variables**... (on the Advanced tab).
7. Under **System Variables**, click **New**
8. Variable Name: _JAVA_OPTIONS.
9. Variable Value: -Xmx512M.
10. Click **OK** to close the dialog.
11. Click **OK** to close **Environment Variables**.
12. Click **OK** to close **System Properties**.
13. Now open Visual Studio.

If this does not resolve the issue, you can upgrade to a 64-bit version of the JDK [from here](http://download.oracle.com/otn-pub/java/jdk/7u79-b15/jdk-7u79-windows-x64.exe) and update the JAVA_HOME environment variable to the new install location.

## More Information
* [Read tutorials and learn about tips, tricks, and known issues](../../cordova-docs-readme.md)
