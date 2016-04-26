<properties pageTitle="Android tips and workarounds"
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

#Android tips and workarounds
This document covers tips, tricks, and known workarounds for problems with the Cordova Android platform.
> **Note**: If your problem is security related, please read [May 26th, 2015 Android Security Issue](../tutorial-cordova-5-readme/)

<a name="deployment"></a>
##Troubleshooting Android build and deployment issues

If you have trouble deploying to Android emulators or devices, see [Resolving build errors](../resolving-build-errors.md#android).

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
