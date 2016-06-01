<properties pageTitle="Known Issues - Plugins Installation"
  description="This is an article on bower tutorial"
  services=""
  documentationCenter=""
  authors="kirupa" />
  <tags ms.technology="cordova" ms.product="Visual Studio 2015"
     ms.service="na"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="mobile-multiple"
     ms.workload="na"
     ms.date="09/10/2015"
     ms.author="kirupac"/>

#**Known Issues - Plugins Installation**

> **Important**: We no longer maintain this article but if you’re stuck, ask us a question on [Stack using the tag ‘visual-studio-cordova'](http://stackoverflow.com/questions/tagged/visual-studio-cordova). Also, subscribe to our [developer blog](http://microsoft.github.io/vstacoblog/). We regularly post issues and workarounds.

This article covers known issues related to Visual Studio Tools for Apache Cordova and installing plugins.




##**Git sourced plugins will not install**
Git sourced plugins will not install properly if you have not installed the [Git command line tools](http://www.git-scm.com/downloads) and do not have the tools in your system path. During installation of the Git tools, select the **Use Git from the Windows Command Prompt** option or add the "bin" folder from the Git install location to your path and restart Visual Studio. (Usually "C:\Program Files (x86)\Git\bin").




##**Git sourced plugins will not install with Cordova 5.1.1 only**
Cordova 5.1.1 has a bug that can cause plugins installed from a Git repo to fail with the error "Error: EXDEV, cross-device link not permitted" if the project is on a different drive than your temp folder.

See [tips and workarounds](../tips-and-workarounds/general/tips-and-workarounds-general-readme.md#plugin-xml) for information on adding plugins that are not present in the config designer. You can add plugins from either the Cordova plugin repository or npm. If you must add a Git version of the plugin, either move your project to the same drive as your temp folder when installing it, or you can instead download a copy, unzip it, and add the plugin from the file system.


##**Plugin with variables not working**
Due to a Cordova issue with Cordova 4.3.0 and 4.3.1, you can run into problems with plugin variables in Cordova versions prior to 5.0.0. Plugin variable information is lost if you install the "plugin" before the "platform" which can happen depending on your workflow. However, plugin variables do function in Cordova 5.1.1, which you can use with Visual Studio 2015. Follow these steps to use a plugin with variables:

 1. Remove the plugins with the variables using the config designer.

 2. Update to Cordova 5.1.1 using the config designer (Platforms > Cordova CLI).

 3. Re-add your plugin via "Plugins" tab in the config designer.



##**Old versions of Cordova plugins due to Cordova plugin ID changes**
A significant change occurred with Cordova 5.0.0+ that also altered the IDs of many core Cordova plugins. The Visual Studio 2015 config designer uses the old IDs (ex: org.apache.cordova.camera not cordova-plugin-camera) with Cordova 4.3.1 and previous versions since the versions of Cordova before 5.0.0 do not support npm.

If you update your Cordova version to 5.1.1 or later, the config designer will automatically switch to using the new IDs. If you do not see this behavior, update Tools for Apache Cordova. If you are an early adopter, you may not see some of the improvments described in this document until after you update since this functionality was enabled in a small post-RTM update. In this case, you will get an update notification soon prompting you to update or, when creating a new project, you can click "Install Tools for Apache Cordova" from the Apache Cordova templates section. Be sure to remove plugins using older IDs from your project before adding the updated plugins with the new IDs.



##**TypeError: Request path contains unescaped characters**
When building or installing a plugin, you may encounter this error if you are using a proxy with certain versions of Node.js and Cordova after a "npm http GET". This is a Cordova issue and the simplest workaround is to downgrade [Node.js 0.10.29](http://nodejs.org/dist/v0.10.29/). This will be resolved in a future version of Cordova. See [tips and workarounds](../tips-and-workarounds/general/tips-and-workarounds-general-readme.md#cordovaproxy) for additional details.



##**Frequent ECONRESET errors from npm when using VPN or proxy**
Is your development machine behind a firewall/proxy server?  If so, you might be running into plugin installation issues because Node.js can experience intermittent issues using SSL to connect to the npm repository with certain versions of Node.js. You can resolve this issue by configuring npm to connect to the registry using straight HTTP instead using this command from the command prompt:

~~~~~~~~~~~~~~~~~~~~~~~
npm config set registry http://registry.npmjs.org
~~~~~~~~~~~~~~~~~~~~~~~



##**Errors from npm related to permission problems**
If you installed Visual Studio or Node.js running as an administrator, you can run into problems where npm attempts to install npm packages under "C:\Program Files (x86)\node.js". You will typically see errors in the Output Window similar to this one:

~~~~~~~~~~~~~~~~~~~~~~~
npm ERR! error rolling back error : EPERM, unlink 'C:\Program Files (x86)\nodejs\vs-tac-cli.cmd'
~~~~~~~~~~~~~~~~~~~~~~~

To resolve this issue, you can re-install Node.js or reconfigure npm using the following commands from the command prompt and then restarting Visual Studio:

~~~~~~~~~~~~~~~~~~~~~~~
npm config set prefix %APPDATA%\npm
npm config set cache %APPDATA%\npm-cache
~~~~~~~~~~~~~~~~~~~~~~~
