---
layout: posts
title: Simple Firefox Context Menu option
---

# Pre-face

Today my mini-project is to add a new Firefox context menu option. The functionality of this option is to perform a dictionary look-up for any word I highlight in Firefox.

This should be something small and simple, using existing Firefox API. However since this is my first time, I would like to document this process and do a "proper learning".

This is a simple illustration of what I'm aiming to do:

![](/static/img/Simple Firefox Context Menu option/README-2020-09-23-12-04-23.png)

# Doing

First, I'll need a project folder.

```sh
mkdir dict-contextmenu
cd dict-contextmenu
```

The first file we need to create is the **manifest.json** file. This file should be present in every extension. It is contains basic metadata about the extension itself, as well as "pointers" to other files in the extension, such as background scripts, content scripts, browser actions..etc.
*(For more details about manifest.json, see here: [manifest.json](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json))*

**manifest.json**
```json
{
    "manifest_version": 2,
    "name": "Perform Dictionary Lookup in new Tab",
    "version": "1.0",

    "description": "Present a right-click context menu option to perform a dictionary search for a word on https://onelook.com/ in a new Tab.",

    "background": {
        "scripts": ["background-script.js"]
    },

    "permissions": ["contextMenus"]
}
```

**Note:**
- The first three keys contain basic metadata for the extension and are mandatory.
  - The `manifest_version` key currently must always be 2. (see [here](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/manifest_version))
- For this extension, we'll be using a Background script.
> Extensions often need to maintain long-term state or perform long-term operations independently of the lifetime of any particular web page or browser window. That is what background scripts are for.
- The `permissions` key is to request special power for the extension. At install time, the browser may inform the user that the extension is requesting certain privileges and ask them to confirm granting these privileges. 

Now, we'll move on to creating that background script,

**background-script.js**
```json
browser.contextMenus.create({
    id: "onelook-lookup",
    title: "Onelook Lookup",

    contexts: ["selection"]
});

browser.contextMenus.onClicked.addListener(async function (info, tab){
    if (info.menuItemId == "onelook-lookup") {
        if(info.selectionText) {
            browser.tabs.create({
                'active': false,
                'index': tab.index+1,
                'url': `https://onelook.com/?w=${info.selectionText}`

            });
        }
    }
})
```

**Note:**
- The `contexts` key contain an array of contexts in which the menu item will appear. For my extension, I only want to look up a word that I have highlighted, so the context would be `"selection"`. *(For the full list of `menus.ContextType`, see [here](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/menus/ContextType).*
- What we did here is:
  - First, we define the new context menu option.
  - Then, we define what it did, by adding an Event listener for `onClicked` which only takes action if the `mnuItemID` matches the context menu option that we created in the previous step.

For the event listener:
- First we define a new variable, which is a new tab. We use the `await` operator which can be used to wait for a `Promise`. This can only be used inside an `async` function. We also don't want to switch to the tab immediately.
- The `addListener` takes two arguments:
  - `info`: Information about the item clicked and the context where the click happened.
  - `tab`: The details of the tab where the click took place.

Let's see if this extension works by installing it temporarily. To do this, we need to go to `about:debugging` and load the `manifest.json` file.

After that:

![](/static/img/Simple Firefox Context Menu option/README-2020-09-23-12-53-59.png)

we click on the option and can see our new inactive tab:

![](/static/img/Simple Firefox Context Menu option/README-2020-09-23-12-54-48.png)

VOILA!

The source for this addon can be found here: https://github.com/akhsiM/firefox-dict-contextmenu.