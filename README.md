# Announcements bar Sharepoint extension

![Preview](./docs/preview.png)

[![Fluent UI React 8.69.0](https://img.shields.io/badge/Fluent%20UI%20React-8.69.0-green.svg)](https://github.com/microsoft/fluentui/blob/master/packages/react/README.md)
[![Mobx 6.6.0](https://img.shields.io/badge/MobX-6.6.0-green.svg)](https://mobx.js.org/)
[![Node.js v14](https://img.shields.io/badge/Node.js-v14-yellow.svg)](https://nodejs.org/en/download/releases/)
[![PnPjs 3.15.0](https://img.shields.io/badge/PnPjs-3.3.2-green.svg)](https://pnp.github.io/pnpjs/)
[![SharePoint Online](https://img.shields.io/badge/SharePoint-Online-yellow.svg)](https://docs.microsoft.com/en-us/sharepoint/introduction)
[![SPFx 1.14.0](https://img.shields.io/badge/SPFx-1.14.0-green.svg)](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/sharepoint-framework-overview)
[![SPFx React Controls 3.8.0](https://img.shields.io/badge/SPFx%20React%20Controls-3.8.0-green.svg)](https://pnp.github.io/sp-dev-fx-controls-react/)
[![Workbench Hosted: Does not work with local workbench](https://img.shields.io/badge/Workbench-Hosted-yellow.svg "Does not work with local workbench")](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/debug-in-vscode#debug-web-part-solution-using-hosted-workbench)
[![Yarn 3.2.1](https://img.shields.io/badge/Yarn-3.2.1-green.svg)](https://yarnpkg.com/)

## Overview

This is an announcements bar for Sharepoint, featuring comments, likes, ratings.

You can:

- **[install & go](#install--go)** to quickly use the extension.
- **[install customized](#install-customized)** to use the extension with a customized setup.
- or read and code as part of the [reusable hybrid repo mvc spfx examples](https://github.com/mauriora/reusable-hybrid-repo-mvc-spfx-examples).

  This one hopefully illustrates:
  - create a model extended from existing SharePoint Announcements for the SharePoint access
  - use out of the box features like likes, comments
  - little specific code needed
  - create separate deployment packages for extension and lists

## Table of contents

- [Announcements bar Sharepoint extension](#announcements-bar-sharepoint-extension)
  - [Overview](#overview)
  - [Table of contents](#table-of-contents)
  - [Install \& go](#install--go)
  - [Install customized](#install-customized)
    - [Changing the list or site name](#changing-the-list-or-site-name)
    - [Upload to the app store](#upload-to-the-app-store)
      - [Lists and content types deployment](#lists-and-content-types-deployment)
      - [Announcements Bar](#announcements-bar)
    - [Hosting site](#hosting-site)
    - [Install the deployment package](#install-the-deployment-package)
      - [Extend an existing announcements list](#extend-an-existing-announcements-list)
      - [Deploy the bar extension](#deploy-the-bar-extension)
    - [Use Powershell to change listname or site name](#use-powershell-to-change-listname-or-site-name)
  - [Minimal path to awesomeness](#minimal-path-to-awesomeness)
    - [Requirements](#requirements)
    - [Install the workspace](#install-the-workspace)
    - [Serve the extension](#serve-the-extension)
    - [Build and install](#build-and-install)
    - [Debug](#debug)
      - [One time trust dev cert](#one-time-trust-dev-cert)
      - [Debug build](#debug-build)
  - [Caveats](#caveats)
    - [Blank page for installation error](#blank-page-for-installation-error)
  - [Content](#content)
    - [Modules](#modules)
    - [Apps](#apps)
      - [Shared](#shared)
    - [Content type hierarchy](#content-type-hierarchy)
  - [Contribute](#contribute)
  - [Create new project](#create-new-project)

## Install & go

1. [In your sharepoint app store](https://YOUR-TENANT.sharepoint.com/sites/apps/AppCatalog/Forms/AllItems.aspx)
`Upload`
    - [app/Announcements-Lists-Deployment/sharepoint/solution/**announcements-lists-deployment**.sppkg](app/Announcements-Lists-Deployment/sharepoint/solution/announcements-lists-deployment.sppkg)
    - [app/Announcements-Bar-Extension/sharepoint/solution/**announcements-bar**.sppkg](app/Announcements-Bar-Extension/sharepoint/solution/announcements-bar.sppkg)
        - Tick the option `Deploy tenant wide`
2. Create a new team site called `news`
    1. On the `site content` page of the `news` site
    2. Click `+ New` -> `App`
        - Find `Announcements Bar lists`
        - Click `Add`
3. With your new `Announcements` list
    1. If you want use likes or ratings, enable them in `Settings` -> `Rating settings`
    2. Add an announcement 🙂

## Install customized

### Changing the list or site name

To be able to deploy the extension tenant wide, without powershell configuration, create the lists
*`Announcements`*  and *`AcknowledgedAnnouncements`* on the site `/sites/news`. If the list names or site name differ, then the Announcements bar needs to be installed/configured using power shell on each site
it's installed. Alternatively you can rebuild the package to suit your defaults.

### Upload to the app store

[Navigate to your sharepoint app store](https://YOUR-TENANT.sharepoint.com/sites/apps/AppCatalog/Forms/AllItems.aspx)

#### Lists and content types deployment

**If** you want to re-use and extend an existing Announcements list **then**, upload:

- [app/Announcements-Model-Deployment/sharepoint/solution/announcements-**model-deployment**.sppkg](app/Announcements-Model-Deployment/sharepoint/solution/announcements-model-deployment.sppkg)

  This contains a list for acknowledged announcements and the extended announcements content type.

**else if** you want to create a new Announcements list **then**, upload:

- [app/Announcements-Lists-Deployment/sharepoint/solution/announcements-**lists-deployment**.sppkg](app/Announcements-Lists-Deployment/sharepoint/solution/announcements-lists-deployment.sppkg)
  
  This contains a list for acknowledged announcements, the announcement list and the extended announcements content type

#### Announcements Bar

Upload [app/Announcements-Bar-Extension/sharepoint/solution/**announcements-bar**.sppkg](app/Announcements-Bar-Extension/sharepoint/solution/announcements-bar.sppkg). If you want the bar to show
on each site of the tenant, tick `Deploy tenant wide`. Otherwise you can install it on each site later by going the sites `Site Content`:

- Click `+ New` -> `App`
- Find `Announcements Bar`
- Click `Add`

### Hosting site

To use the like/rating feature, a team site is required, otherwise a communication site will be fine.
The hosting site must host both lists, `Announcements` and `AcknowledgedAnnouncements`.
By default the hosting site is `sites/news`.

### Install the deployment package

On the `site content` page of the site hosting the Announcements:

1. Click `+ New` -> `App`
   - Find `Announcements Bar lists` or `Announcements Bar model`
      - Click `Add`

#### Extend an existing announcements list

Follow this if you've chosen the `Announcements Bar model` deployment package.

Open `Settings` for the *announcement* list

- In `Advanced settings`
  - Enable management of content types
  - Disable attachments
- In `Add from existing content types`
  - Add `Announcements Extended`
- Click on the default item content type (or other pre-existing)
  - delete this content type
- Optional for ratings (this only works on team sites)
  - Select `Rating settings`
    - Rating Settings: **Yes** or (**No** if you want to disable ratings)
    - Which voting/rating experience you would like to enable for this list?
      - `Likes` if you want to show the Thumbs icon and count
      - `Star Ratings` if you want to show the 5-star rating

#### Deploy the bar extension

[In your sharepoint app store](https://YOUR-TENANT.sharepoint.com/sites/apps/AppCatalog/Forms/AllItems.aspx)
`Upload`
[app/Announcements-Bar-Extension/sharepoint/solution/announcements-bar.sppkg](app/Announcements-Bar-Extension/sharepoint/solution/announcements-bar.sppkg)

Select [x] `Deploy tenant wide` if you used the above default settings and want the bar to appear on every modern site.

### Use Powershell to change listname or site name

If you didn't host Announcements on `sites/news`, and or used different names for the lists, then you need to open PowerShell to add the extension to each site:

1. `Connect-PnPOnline -Url https://YOUR-TENANT.sharepoint.com/sites/YourSite`
2. Modify the `{'listName': 'Announcements', 'acknowledgedListName': 'AcknowledgedAnnouncements', 'siteUrl': '/sites/News'}` to point to your site containing your announcements *announcement* list and *acknowledged* list from earlier:

    Depending on your pnp / powershell version, the `ClientSideComponentProperties` need to be escaped different:

    ``Add-PnPCustomAction -Name "Mauri-Ora-Announcements-Bar" -Title "Mauri-Ora-Announcements-Bar" -Location "ClientSideExtension.ApplicationCustomizer" -ClientSideComponentId "f4e7b2fc-be9a-43de-bb92-4a67c3f72793" -ClientSideComponentProperties "{ `"listName`": `"Announcements`", `"acknowledgedListName`": `"AcknowledgedAnnouncements`", `"siteUrl`": `"/sites/News`"}"``

    or

    ``Add-PnPCustomAction -Name "Mauri-Ora-Announcements-Bar" -Title "Mauri-Ora-Announcements-Bar" -Location "ClientSideExtension.ApplicationCustomizer" -ClientSideComponentId "f4e7b2fc-be9a-43de-bb92-4a67c3f72793" -ClientSideComponentProperties "{'listName': 'Announcements', 'acknowledgedListName': 'AcknowledgedAnnouncements', 'siteUrl': '/sites/News'}"``

If you have issues, check the browser logs, `Announcements onInit` logs an object with an `properties` object containing `listName`, `acknowledgedListName` and `siteUrl`.

## Minimal path to awesomeness

[Upload to the app store](#upload-to-the-app-store) the [lists and content types deployment](#lists-and-content-types-deployment) package of your choice. On your [Hosting site](#hosting-site) [install the deployment package](#install-the-deployment-package)

### Requirements

You should have the following installed:

- Code editor
- Node 14
- yarn 2+
- GitExtension (the program, if your contributing or forking a new project of this)

### Install the workspace

```shell
git clone --recurse-submodules https://github.com/mauriora/Announcements-Bar-Spfx.git
cd Announcements-Bar-Spfx
yarn install
yarn build
code .
```

### Serve the extension

Initially, please follow the steps in [Debug](Debug) first. Then:

Edit [app\Announcements-Bar-Extension/config/serve.json](app/Announcements-Bar-Extension/config/serve.json)

- set *pageUrl* to the page you want to test your extension on
- in properties, set siteurl to the [Hosting site](#hosting-site)

open a terminal in the solution (root) folder

```shell
yarn serve
```

a browser will open and navigate to *pageUrl*

### Build and install

1. In a solution terminal execute `yarn build`
2. Either [Install & go](#install--go) or [Install customized](#install-customized)

### Debug

Before you can serve the extension, a debug-build needs to be installed. The first time you debug using `serve` the local developer certificate needs to be trusted.

#### One time trust dev cert

In [app/Announcements-Bar-Extension](app/Announcements-Bar-Extension) execute in a terminal:

```shell
    yarn run trust-dev-cert
```

#### Debug build

The debug build basically uploads only the manifest, the code will be loaded from the local machine which is running `serve`.
In [app/Announcements-Bar-Extension](app/Announcements-Bar-Extension) execute in a terminal:

```shell
    yarn run build-debug
```

Either [Install & go](#install--go) or [Install customized](#install-customized)

## Caveats

### Blank page for installation error

To see the installation errors, go to `Site content` using the `classic experience`.
Locate the failed package and:

- click the more menu (`...`)
- Click `DETAILS`
- In the `Errors` section
  - Click on the number to the right of `Install Errors`
  - A balloon popup should appear showing the error(s)

If you see a blank page, open `PowerShell` with `pnp` and execute:

```shell
    Connect-PnPOnline -Url "https://<YOURTENANT>-admin.sharepoint.com" -interactive
    Set-PnPTenantSite -Identity "https://<YOURTENANT>.sharepoint.com/sites/<YOURSITE>" -DenyAddAndCustomizePages:$false
```

## Content

### Modules

![Modules](./docs/Modules.svg)

### Apps

These are the actual "outputs" `.sppkg`.

`Announcements-Bar-Extension` is the visible react extension, either installed tenant wide or per site.

`Announcements-Lists-Deployment` is the extended announcements list package usually deployed on one site per tenant.

`Announcements-Model-Deployment` is the deployment of just the content type, if you want to extend an existing Announcements list.

#### Shared

These modules usually change during the cause of development. It might be that they are completely re-used and not modified at all. The hybrid repro structure makes it easy to quickly enhance commonly used modules.
`Model-Announcement-Extended` has been created with this project, but could be used in others as well.

### Content type hierarchy

![Content type hierarchy diagram](./docs/Content-Type.svg)

## Contribute

Use the minimal path to awesomeness and please create a fork and branch for your contribution.
Then do a pull request to merge your branch into the main branch.

## Create new project

1. Fork the solution as *YOUR-SOLUTION*
2. Clone the forked solution
3. Fork the starting app project as *YOUR-PROJECT*
4. Add the *YOUR-PROJECT* as submodule to *YOUR-SOLUTION* using the app GitExtension, manage sub modules
5. Remove the starting project, and any other you don't need, from YOUR-solution using the app GitExtension, manage sub modules
6. call `yarn generateNewGuidsResetVersions` in apps/*YOUR-PROJECT*
7. rename *YOUR-.....* in:
    - app/*YOUR-PROJECT*
        - package.json
        - src/extensions/*YOUR-EXTENSION*
            - *YOUR-EXTENSION*.manifest.json
        - config
            - config.json
            - package-solution.json
        - sharepoint/assets/*
8. Copy the *id* value from *YOUR-EXTENSION*.manifest.json as *componentId*, and the *alias* value as *title*  to
   - config
      - serve.json
   - sharepoint/assets/
      - elements.xml
      - ClientSideInstance.xml
