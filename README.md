# Introduction 
This is an example solution of a SharePoint MVC implementation using PNPjs, Spfx-Controls and Fluent UI.
It hopefully illustrates:
- create a model for the SharePoint access
- reusable modules for the basics, data source access and presentation
- focus on business logic development
- an extendable framework by YOUR contribution, every contribution counts

# Table of contents
1. [Getting Started](#getting-started)
    1. [Requirements](#requirements)
    2. [Minimal path to awesomeness](#minimal-path-to-awesomeness)
3. [Build and install](#build-and-install)
4. [Contribute](#contribute)
    1. [To do list](#to-do-list)
5. [Create new project](#create-new-project)

# Getting Started
You can use the "Minimal path to awesomeness" to have look around. Please brnach before contributing.
You can follow the "Create a new project" to start with a copy of this a starting point for a new project (usually app)

## Requirements
You should have the following installed:
- Visual Studio Code (or similar)
- Node 14
- yarn
- GitExtension (the program, if your contributing or forking a new project of this)

## Minimal path to awesomeness

### Create the content type
Create a new content type as child of the builtin Announcement.
Add from existing site columns:
- Owner (ReportOwner) ?
- Start Date (StartDate)
- Urgent ?
- URL (URL)

If you can't create content-types, add the above, after you created the list with the Announcements content type. If you can find all the columns in existing ones, some features might be missing. You can add them manually.

### Create the list
1. To be able to deploy the extension tenant wide without configuration, create the list *__Announcements__* on the site *__/sites/news__*
2. Open *__SiteContent__* on the site that will host the _Announcements_ list containing the announcements
3. Create the _announcement_ list
4. In the settings:
    - Advanced settings
        - Enable management of content types
        - Disable attachments
    - Add from existing content types
        - Add your previous created content type. If it doesn't show, wait for up to 4 hours
    - Click on the default item content type
        - delete this content type
    - Rating settings (this is seems to only appear on team sites)
        - Rating Settings: Yes
        - Which voting/rating experience you would like to enable for this list?: Likes

### Serve the extension
```
yarn global add lerna
git clone --recurse-submodules https://github.com/mauriora/Rateable-Announcements-Solution.git
cd Rateable-Announcements-Solution
lerna bootstrap
code .
```
Edit `app\Rateable-Announcements-Extension\config\serve.json`
 - set *pageUrl* the spage you want to test your extension on
 - in properties, set listname and siteurl
open a terminal in the solution (root) folder
```
yarn build-shared
yarn serve
```
a browser will open and navigate to *pageUrl*

# Build and install
For the likes to work, the list needs to be on a site with option to enable ratings on lists.

1. In a solution terminal execute `yarn build-app`
2. [browse to the sharepoint app store on *YOUR-TENANT*](https://YOUR-TENANT.sharepoint.com/sites/apps/AppCatalog/Forms/AllItems.aspx)
3. Click **Upload**
4. Click **Choose files**
5. Navigate to *YOUR-SOLUTION*/apps/*YOUR-PROJECT*/sharepoint/solution
6. Select *YOUR-PROJECT*.sppkg and click **Open**
7. Add a comment and click **OK**
8. Wait for the upload to finish and a dialog to open
9. If you want the EXTENSION to be available on all sites without installing, tick **Make this solution available to all sites in the organization**, otherwise it needs to be installed on each site using
10. Click **Deploy**
6. If you didn't deploy tenant wide:
   1. go to the *__site content__* of the site you want to add the announcement toolbar
   2. Click *__+ New ˅__*
   3. Select *__App__*
   4. Select *__Mauri-Ora-Rateable Annoncements__*
7. If you didn't host Announcements on sites/news, then you need to open PowerShell to add the extension to each site <YourSiteUrl>
   1. ` Connect-PnPOnline -Url <YourSiteUrl>`
   2. Modify the `{'listName': 'Announcements', 'siteUrl': '/sites/News'}` to point to your site containing your announcements list from steps 1 and 2:
    
      Depending on your pnp / powershell version, the _ClientSideComponentProperties_ needs to be escaped different.
    
      If you have issues, check the browser logs, "Announcements onInit" logs an object with an properties object containing _listName_ and _siteUrl_
      - `` Add-PnPCustomAction -Name "Mauri-Ora-Rateable-Announcements" -Title "Mauri-Ora-Rateable-Announcements" -Location "ClientSideExtension.ApplicationCustomizer" -ClientSideComponentId "188a73d9-a68e-381e-4625-a5f0cf7c0024" -ClientSideComponentProperties "{'listName': 'Announcements', 'siteUrl': '/sites/News'}" ``
      - `` Add-PnPCustomAction -Name "Mauri-Ora-Rateable-Announcements" -Title "Mauri-Ora-Rateable-Announcements" -Location "ClientSideExtension.ApplicationCustomizer" -ClientSideComponentId "188a73d9-a68e-381e-4625-a5f0cf7c0024" -ClientSideComponentProperties "{ `"listName`": `"Announcements`", `"siteUrl`": `"/sites/News`"}" ``

13. Create a page and add **YOUR-EXTENSION**

# Contribute
Use the minmal path to awesomeness and please create a branch for your contribution.
Then do a pull request to merge your branch into the main branch.

## To do list

### Modules
1. [ ] Utils module
1. [ ] Theming/branding module per client


# Create new project
1. Fork the solution as *YOUR-SOLUTION*
2. Clone the forked solution
3. Fork the starting app project as *YOUR-PROJECT*
4. Add the *YOUR-PROJECT* as submodule to *YOUR-SOLUTION* using the app GitExtenson, manage sub modules
5. Remove the starting project, and any other you don't need, from YOUR-solution using the app GitExtenson, manage sub modules
6. call `yarn initguids` in apps/*YOUR-PROJECT*
6.a replace guids in server.json, elements.xml and  clientsideinstances.xml
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
   - sharepoint/assets/
      - elements.xml
      - ClientSideInstance.xml

