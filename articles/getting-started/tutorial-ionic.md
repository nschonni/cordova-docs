
<properties pageTitle="Getting started with Ionic apps in Visual Studio"
  description="This is an article on ionic tutorial"
  services=""
  documentationCenter=""
  authors="mikejo5000" />
  <tags
     ms.service="na"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="mobile-multiple"
     ms.workload="na"
     ms.date="09/10/2015"
     ms.author="mikejo"/>

# Get started with Ionic apps in Visual Studio
[Ionic](http://www.ionicframework.com) is a popular front-end JavaScript framework for developing cross-platform mobile apps using Cordova. You can use Visual Studio 2015 and the Ionic CLI to easily create and debug cross-platform apps.

>**Note**: For a video walkthrough that shows similar steps, see the [Video tutorial](http://go.microsoft.com/fwlink/p/?LinkID=534728).

## Set up your machine for Ionic with VS <a name="getStarted"></a>

To follow these steps, you must:

1. [Install Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkID=533794).

    When you install Visual Studio, make sure you include the optional components, **HTML/JavaScript (Apache Cordova)** under Cross-Platform Mobile Development.

2. Make sure your Visual Studio setup is correct by creating and running the default Blank App template.

    * In Visual Studio, choose **File**, **New**, **Project**, **JavaScript**, **Apache Cordova Apps**, **Blank App** and name the new project "blank".

    * Choose **Windows**, **Local Machine** and press F5 to run the app (make sure the app loads correctly). If any issues occur, see [Configure the Tools] (configure-vs-tools-apache-cordova.md).  

3. [Install the Ionic CLI](http://ionicframework.com/docs/cli/install.html).

    ```
    npm install -g ionic
    ```

## Get the Ionic starter app templates <a name="getTemplates"></a>

1. Make sure you installed the Ionic CLI, then open a command line.
2. Go to the directory where you want to install the Ionic starter app templates, such as the Documents folder.

    ```
    C:\\Users\<username>\Documents\ionicMySideMenu>
    ```

3. In the command line, type

    ```
    ionic start ionicMySideMenu sidemenu
    ```

    Ionic creates the project in your current folder.

4. Use the same command to install more templates, such as:

    ```
    ionic start ionicMyTabs tabs
    ionic start ionicMySlide blank
    ```
>**Note**: If you are trying to use a Visual Studio 2013 Ionic project in Visual Studio 2015 (recommended), see this info on [migrating projects](migrate-to-vs2015.md) to Visual Studio 2015.

## Modify the project to work with VS <a name="configTemplates"></a>

For each of the Ionic starter app templates that you installed and want to run, do this:

1. In Visual Studio, choose **File**, **New**, **Project From Existing Code**.
2. In the wizard, choose **Apache Cordova** as the project type that you'd like to create, and then choose **Next**.

    ![Create a project from existing code](media/tutorial-ionic/ionic-create-wizard.png)

3. For the **Project file location**, browse to the root folder of the Ionic project and select it.

    ![Select the source project](media/tutorial-ionic/ionic-create-specify-root.png)

4. Type a name for your new project and select **Finish**.

    Visual Studio creates the new project. Wait for Bower/NPM updates to the project to finish, if necessary.

## Get your app running on Android <a name="configAndroid"></a>

1. If you want to use the Ionic CLI to add the Android platform, use this command in the command line:

    ```
    ionic platform add android
    ```

    Or, you can add the platform by building in VS (choose **Build** > **Build Solution**).

2. Choose **Android** as a debug target (Solution Platforms list), and to get the app running choose a target such as Ripple (Chrome required) or the **VS Emulator 5" KitKat (4.4)** (Hyper-V required).

    ![Run the app](media/tutorial-ionic/ionic-f5.png)

3. Press F5, and the app should load correctly.

    ![App running in the emulator](media/tutorial-ionic/ionic-sidemenu.png)

  >**Note**: If you have previously run the VS Emulator for Android and you have errors, try deleting the emulator VM instance in Hyper-V Manager. Otherwise, if you have errors see [Troubleshooting] (https://msdn.microsoft.com/en-us/library/mt228282).

## Get your app running on iOS <a name="configiOS"></a>

  You can run initially on the Ripple Emulator after selecting iOS as a debug target, but for detailed info on setting up the remotebuild agent for iOS, see [this topic](install-vs-tools-apache-cordova.md#ios).

  The Ionic starter app should run correctly on iOS when the remotebuild agent is running on a Mac, and when Visual Studio is configured to connect to it. (The complete steps are outside the scope here.)

## Get your app running on Windows 10

To target Windows 10 in your app, you need to:

1. Use the Visual Studio install program to install the **Universal Windows App Development Tools** (optional software).

2. In the Platforms tab of the configuration designer, enter 5.3.1 (or 5.1.1) as the Cordova version.

3. Choose **Build Solution** from the **Build** menu.

4. In the configuration designer, choose Windows, and in the **Windows Target Version**, choose  **Windows 10.0**, and save changes.

5. Choose a Windows 10 deployment target, such as **Mobile Emulator 10.0.xxxxx.0 WVGA 4 inch 1GB**.

6. Press F5 to run your app.

## Get your app running on Windows 8 or 8.1 <a name="configWindows"></a>

1. Open the folder for the Blank App project created in Visual Studio and copy the merges folder (and its contents) to your Ionic project. Copy the folder under the top level folder (for example, under ionicMySideMenu folder).

    When you complete the next few steps, you will resolve errors loading partial pages by using the winstore-jscompat.js shim.

2. In the Ionic project, create a **scripts** folder under www, and copy platformOverrides.js from the Blank App project's `www\scripts` folder to the new scripts folder.

    ![Copy platformsOverrides.js to the new scripts folder](media/tutorial-ionic/ionic-platform-overrides.png)

3. In the Ionic project, add the following script reference to index.html, just before the Ionic Framework reference (before the ionic.bundle.js reference):

    ```
    <script src="scripts/platformOverrides.js></script>
    ```

    >**Note**: If you use the js folder instead of scripts, the folder and path will need to be updated in several other files.

4. Select **Windows** or **Windows Phone (Universal)** as a debug target (Solution Platforms list).

  * For Windows, choose **Local Machine** as the deployment target (if you are running on Windows 8.1).

  * For Windows Phone 8.1, choose one of the Emulator 8.1 options.

5. Press F5 to start debugging.

    > **Note**: If you see the TypeScript error or the Keyboard plugin error, see the previous steps for Android to resolve.

## Common issues and workarounds

A few errors are fairly common in the starter templates when debugging in Visual Studio.

### Keyboard plugin not found?

In some of the Ionic starter app templates, you may get an error on this line of code.

```
if (window.cordova && window.cordova.plugins.Keyboard) {
  ...
}
```

To fix, open the Plugins tab of the configuation designer, and choose Custom, and then Git repository.

Add the following git URL and rebuld.

```
https://github.com/driftyco/ionic-plugins-keyboard.git
```

If you add the plugins using the configuration designer, Visual Studio can recognize the plugin and add the plugin reference to config.xml.

> **Note**: Some APIs used in the templates for the Keyboard plugin are iOS only, like `hideKeyboardAccessoryBar`.

### TypeScript errors?

In some of the Ionic starter app templates, you may also need to remove the TypeScript file, angular-ui-router.d.ts, for the angular-ui-router module, or you may see this error.

![TypeScript error](media/tutorial-ionic/ionic-typescript-errors.png)

> **Note** If you are using TypeScript, you need to get updated d.ts files or an updated version of the template to support the routing module.

### WWAHost runtime error? <a name="win10tips"></a>

When debugging on a Windows 8.1 dev machine, you may get a WWAHost runtime error when navigating between pages in Ionic apps. You can work around this by:

* Closing DOM Explorer before navigating pages, or

* Upgrading to Windows 10 on your dev machine (issue is fixed in Win10).

### Partial pages don't load?

When you are using the AngularJS routing module (Ionic starter templates often use this module), you may need to include a call to `aHrefSanitizationWhitelist`.

If you see the dialog box shown here, you have likely run into this issue.

![partial pages don't load](media/tutorial-ionic/ionic-error-partial-pages.png)

Typically, you include the code fix in app.js or wherever you are calling your module configuration code:

```
$compileProvider.aHrefSanitizationWhitelist(/^\s*(https?|ftp|mailto|file|ghttps?|ms-appx|ms-appx-web|x-wmapp0):/);

$compileProvider.imgSrcSanitizationWhitelist(/^\s*(https?|ftp|file|ms-appx|ms-appx-web|x-wmapp0):|data:image\//);
```
If you use the preceding code, then add a reference to $compileProvider in the following line of code.

```
.config(function ($stateProvider, $urlRouterProvider) {
  ```

so that it looks like this:

```
.config(function ($compileProvider, $stateProvider, $urlRouterProvider) {
  ```

### Unhandled exception running on Windows?

If you see the following unhandled exception when targeting Win/WinPhone 8.1, follow the [earlier steps](#configWindows) to call platformOverrides.js to fix this issue.

![unhandled exception](media/tutorial-ionic/ionic-unhandled-exception.png)

If you see the same error when targeting Windows 10, make sure you set **Windows 10.0** as the target in the Windows tab of the configuation designer.
