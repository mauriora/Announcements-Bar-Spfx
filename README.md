# Announcements bar Sharepoint extension

## Introduction

This is an example implementation of an announcements bar in the Sharepoint, featuring out of the box features like likes.
Here you can:

- [install configure go](#install-configure-go) to use the extension.
- or read and code as part of the [reusable hybrid repo mvc spfx examples](https://github.com/mauriora/reusable-hybrid-repo-mvc-spfx-examples).

  This one hopefully illustrates:
  - create a model extended from existing SharePoint Announcements for the SharePoint access
  - use out of the box features like likes, comments
  - little specific code needed
  - deploy a list and content type

## Table of contents

1. [Getting Started](#getting-started)
   1. [Install configure go](#install-configure-go)
   2. [Minimal path to awesomeness](#minimal-path-to-awesomeness)
2. [Build and install](#build-and-install)
3. [Caveats](#caveats)
   1. [Blank page for installation error](#blank-page-for-installation-error)
4. [Details](#details)
   1. [Content type](#content-type)
5. [Contribute](#contribute)
6. [Create new project](#create-new-project)

## Getting Started

- [Install configure go](#install-configure-go) to use the extension
- Follow the [Minimal path to awesomeness](#minimal-path-to-awesomeness) to have look around.
You can follow the "Create a new project" to start with a copy of this a starting point for a new extension project.

### Install configure go

> To be able to deploy the extension tenant wide without configuration, create the lists
> *`Announcements`*  and *`AcknowledgedAnnouncements`* on the site `/sites/news`

#### Deploy lists

[In your sharepoint app store](https://YOUR-TENANT.sharepoint.com/sites/apps/AppCatalog/Forms/AllItems.aspx)
`Upload` [app/Announcements-Model-Deployment/sharepoint/solution/announcements-model-deployment.sppkg](app/Announcements-Model-Deployment/sharepoint/solution/announcements-model-deployment.sppkg)

On the `site content` page of the site hosting the Announcements:

1. Click `+ New` -> `App`
   - Find `Announcements Bar list and content type`
      - Click `Add`
2. If you want to create a new _announcement_ list, click `+ New` -> `List`
   1. Enter a name for the _announcement_ list
      > strongly recommended to use `Announcements` and change the _displayed_ name later.
   2. Click `Create`
3. Open `Settings` for the _announcement_ list
    - In `Advanced settings`
        - Enable management of content types
        - Disable attachments
    - In `Add from existing content types`
        - Add `Announcements Extended`
    - Click on the default item content type (or other pre-existing)
        - delete this content type
    - Optional for ratings (this seems to only appear on team sites)
        - Select `Rating settings`
            - Rating Settings: **Yes** or (**No** if you want to disable ratings)
            - Which voting/rating experience you would like to enable for this list?
                - `Likes` if you want to show the Thumbs icon and count
                - `Star Ratings` if you want to show the 5-star rating

#### Deploy the bar extension

[In your sharepoint app store](https://YOUR-TENANT.sharepoint.com/sites/apps/AppCatalog/Forms/AllItems.aspx)
`Upload` [app/Announcements-Bar-Extension/sharepoint/solution/announcements-bar.sppkg](app/Announcements-Bar-Extension/sharepoint/solution/announcements-bar.sppkg)

Select [x] `Deploy tenant wide` if you used the above default settings and want the bar to appear on every modern site.

### Minimal path to awesomeness

[Deploy lists](#deploy-lists)

#### Requirements

You should have the following installed:

- Code editor
- Node 14
- yarn
- GitExtension (the program, if your contributing or forking a new project of this)

#### Serve the extension

```shell
git clone --recurse-submodules https://github.com/mauriora/Announcements-Bar-Spfx.git
cd Announcements-Bar-Spfx
yarn
code .
```

Edit `app\Announcements-Bar-Extension\config\serve.json`

- set *pageUrl* to the spage you want to test your extension on
- in properties, set listname and siteurl

open a terminal in the solution (root) folder

```shell
yarn build-shared
yarn serve
```

a browser will open and navigate to *pageUrl*

## Build and install

For the likes to work, the list needs to be on a site with option to enable ratings on lists.

1. In a solution terminal execute `yarn build-release`
2. [browse to the sharepoint app store on *YOUR-TENANT*](https://YOUR-TENANT.sharepoint.com/sites/apps/AppCatalog/Forms/AllItems.aspx)
3. Click **Upload**
4. Click **Choose files**
5. Navigate to *YOUR-SOLUTION*/apps/*YOUR-PROJECT*/sharepoint/solution
6. Select *YOUR-PROJECT*.sppkg and click **Open**
7. Add a comment and click **OK**
8. Wait for the upload to finish and a dialog to open
9. If you want the EXTENSION to be available on all sites without installing, tick **Make this solution available to all sites in the organization**, otherwise it needs to be installed on each site using
10. Click **Deploy**
11. If you didn't deploy tenant wide:
    1. go to the *__site content__* of the site you want to add the announcement toolbar
    2. Click *__+ New ˅__*
    3. Select *__App__*
    4. Select *__Mauri-Ora-Rateable Annoncements__*
12. If you didn't host Announcements on sites/news, then you need to open PowerShell to add the extension to each site
    1. `Connect-PnPOnline -Url https://YOUR-TENANT.sharepoint.com/sites/YourSite`
    2. Modify the `{'listName': 'Announcements', 'acknowledgedListName': 'AcknowledgedAnnouncements', 'siteUrl': '/sites/News'}` to point to your site containing your announcements _announcement_ list and _acknowledged_ list from earlier:

        Depending on your pnp / powershell version, the `ClientSideComponentProperties` need to be escaped different:

        ``Add-PnPCustomAction -Name "Mauri-Ora-Announcements-Bar" -Title "Mauri-Ora-Announcements-Bar" -Location "ClientSideExtension.ApplicationCustomizer" -ClientSideComponentId "f4e7b2fc-be9a-43de-bb92-4a67c3f72793" -ClientSideComponentProperties "{ `"listName`": `"Announcements`", `"acknowledgedListName`": `"AcknowledgedAnnouncements`", `"siteUrl`": `"/sites/News`"}"``

        or

        ``Add-PnPCustomAction -Name "Mauri-Ora-Announcements-Bar" -Title "Mauri-Ora-Announcements-Bar" -Location "ClientSideExtension.ApplicationCustomizer" -ClientSideComponentId "f4e7b2fc-be9a-43de-bb92-4a67c3f72793" -ClientSideComponentProperties "{'listName': 'Announcements', 'acknowledgedListName': 'AcknowledgedAnnouncements', 'siteUrl': '/sites/News'}"``

        If you have issues, check the browser logs, "Announcements onInit" logs an object with an properties object containing _listName_, _acknowledgedListName_ and _siteUrl_

13. Create a page and add **YOUR-EXTENSION**

## Caveats

### Blank page for installation error

To see the installation errors, go to `Site content` using the `classic experience`.
Locate the failed package and:

- click the more menu (`...`)
- Click `DETAILS`
- In the `Errors` section
  - Click on the number to the right of `Install Errors`
  - A baloon popup should appear showing the error(s)

If you see a blank page, open `PowerShell` with `pnp` and execute:

```shell
    Connect-PnPOnline -Url "https://<YOURTENANT>-admin.sharepoint.com" -interactive
    Set-PnPTenantSite -Identity "https://<YOURTENANT>.sharepoint.com/sites/<YOURSITE>" -DenyAddAndCustomizePages:$false
```

## Details

### Content type hierarchy

![Content type hierarchy diagram](./docs/Content-Type.svg)

## Contribute

Use the minmal path to awesomeness and please create a fork and branch for your contribution.
Then do a pull request to merge your branch into the main branch.

## Create new project

1. Fork the solution as *YOUR-SOLUTION*
2. Clone the forked solution
3. Fork the starting app project as *YOUR-PROJECT*
4. Add the *YOUR-PROJECT* as submodule to *YOUR-SOLUTION* using the app GitExtenson, manage sub modules
5. Remove the starting project, and any other you don't need, from YOUR-solution using the app GitExtenson, manage sub modules
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
