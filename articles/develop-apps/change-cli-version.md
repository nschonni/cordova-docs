<properties
   pageTitle="Not sure yet | Cordova"
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
   ms.date="12/01/2015"
   ms.author="normesta"/>

# Change the CLI version of your Visual Studio Tools for Apache Cordova project

Visual Studio uses the Cordova Command-Line Interface (CLI) to build your project and start platform emulators.

Over time, Apache Cordova releases new versions of the CLI that include bug fixes and other improvements. You can update your project to use these new versions as they are released.

This topic helps you understand the impact of making that change, how you can do that safely.

## Find the CLI version number of your project

The CLI version number appears in the **Platforms** page of the configuration designer.

![CLI version](media/change-cli-version/cli-version.png)

When you first create your project, Visual Studio uses the most recent *major* version of the CLI, but over time, this version number becomes outdated. If you want to use a more recent version of the CLI, you have to make this change manually.

Let's take a quick look at how this change can impact plugins. This will help you decide if you want to make the change, and how to do it safely.

## The impact on plugins

Plugins are tested against a specific version of each Cordova platform. For example, to ensure that the plugin works on an Android mobile device, the author tests and validates the plugin against the *cordova-android 5.0.0* platform. In a sense, it's tied to that version of the Android platform.

The Apache Cordova CLI does something very similar. It's also tied or *pinned* to a specific version of each Cordova platform. When you first create a project, your CLI and the plugins that you add to your project are tied to the same versions.

If you update your CLI, that CLI tied to a newer version of each Cordova platform while your plugins remain tied to a previous version of each Cordova platform.

This is not always a problem, but if a new version introduces a breaking change, you might encounter errors when you build your project or attempt to run code that uses the plugin.

You might encounter the opposite problem, if you don't update the CLI version of your project. Plugins that already exist in your project work fine, but plugins that you add to your project might be tied to newer version of each Cordova platform.  

Have a quick look at this table. It presents each action, its impact, and what you can do to increase the likelihood that your plugins will function correctly after you've taken that action.

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
    <thead>
        <tr>
            <th>Action</th>
            <th style="text-align:left">Impact on plugins</th>
            <th style="text-align:left">What you can do</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Use the most recent version of the CLI</td>
            <td style="text-align:left">Plugins that exist in your project before you update the CLI version might not work as expected if they are tied to an older version of each Cordova platform.</td>
            <td style="text-align:left">Remove those plugins from your project.  Then, add the most recent version of those plugins to your project.</td>
        </tr>
        <tr>
            <td>Continue using the same version of the CLI</td>
            <td style="text-align:left">Plugins that you add to your project might not work as expected unless you explicitly add an older version of the plugin. </td>
            <td style="text-align:left">If no breaking changes were introduced in newer versions of each Cordova platform then plugins might work as expected.<br><br>If you encounter unexpected behaviors in code that use those plugins, just add older versions of plugins to your project. </td>
        </tr>
        <tr>
            <td>Use an older version of the CLI</td>
            <td style="text-align:left">All plugins (new or existing) might not work as expected.<br><br></td>
            <td style="text-align:left">Remove existing plugins from your project and then add an older version of each plugin to your project.
            <br><br>If you want to add any new plugins to your project, add older version of those plugins as well. </td>
        </tr>
    </tbody>
</table>

## How to update the CLI version of your project

1. First, back up any file that you directly modified in the **platforms** folder of your project.

    Visual studio will remove the **platform** folder and any file inside of it when you build your project. The following image shows this folder.

    ![CLI version](media/change-cli-version/platforms.png)

    Most likely, you have not made direct edits to this folder. Editing these files is an advanced task and you'd have to make those edits outside of Visual Studio.  

    If you did not directly edit these files outside of Visual Studio, you can move to the next step.

2. In **Solution Explorer**, double-click the **config.xml** file to open the configuration designer.

    ![CLI version](media/change-cli-version/config-xml.png)

3. In the configuration designer, choose the **Platforms** tab, and then, in the **Cordova CLI** drop-down list, choose the version of the Cordova CLI that you want to use.

    ![CLI version](media/change-cli-version/config-designer.png)

4. In Visual Studio, choose **Build**->**Rebuild Solution**.

    This removes all subfolders from the **Platform** folder of your project and then adds a new subfolder for each platform.

5. Add back any manual tweaks to files in platform subfolders.

6. Remove all plugins from your project.

    To remove core plugins, see [Remove a plugin](./develop-apps/manage-plugins.md#Adding).

    To remove other third-party plugins, see [Remove a plugin that is not present in the configuration designer](./develop-apps/manage-plugins.md#AddOther).

7. Add plugins back to your project.

    If you updated your project to use the most recent version of the CLI, then add the most recent version of each plugin to your project. If you updated your project to use an older version of the CLI, then add older versions of each plugin to your project.

    To add core plugins, see [Add a plugin](./develop-apps/manage-plugins.md#Adding).

    To add other third-part plugins, see [Add a plugin that is not present in the configuration designer](./develop-apps/manage-plugins.md#AddOther).
