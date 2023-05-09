---
layout: "layouts/doc-post.njk"
title: "What are extensions?"
date: 2013-02-21
updated: 2022-07-22
description: An overview of the purpose of Chrome Extensions and how they're developed.
---

This page provides a brief introduction to Chrome extensions and walks through the creation of a
"Hello, World!" extension.


## About extensions {: #intro }

Extensions are small software programs that customize the browsing experience. They let users tailor
Chrome functionality and behavior in many ways, providing things like:

* Productivity
* Web page content enrichment
* Information aggregation
* Fun and games

These are just a few examples of the many things that extensions can do. Visit the [Chrome Web
Store][cws] to see thousands of different examples of published extensions.

### How do extensions work? {: #basics }

Extensions are built on web technologies such as HTML, JavaScript, and CSS.  They run in a separate,
sandboxed execution environment and interact with the Chrome browser.

{# TODO: put a diagram here that is useful and helps with context #}
{% if false %}

Consider the context for this
-- Chrome runs on a device, connecting the user to resources on the Web, as depicted in this context
diagram:

{% Img src="image/SHhb2PDKzXTggPGAYpv8JgR81pX2/5qVlqGn6vVMCWCSRclOY.png", alt="Context diagram of Chrome with an extension", width="517", height="476" %}

Also shown in the diagram is an extension "plugged into" the Chrome browser. The extension can
access and modify selected resources and actions in Chrome. There are two aspects to consider:

{% endif %}

Extensions let you "extend" the browser by using APIs to modify browser behavior and access web
content. Extensions operate by means of an end-user UI and a developer API:

**The extensions user interface:** This provides a consistent way for users to manage their
extensions.

**Extensions APIs:** The [extensions APIs](/docs/extensions/reference/) allow the extension's
code to access features of the browser itself: activating tabs, modifying net requests, and so on.

To create an extension, you assemble some resources&mdash;a manifest, JavaScript and HTML files,
images, and others&mdash;that constitute the extension. For development and testing, you can load these
"unpacked" into Chrome using [extension developer mode][devmode]. Once you are happy with your
extension, you can [package it and distribute it to users][cws-publish].

### How do users get extensions? {: #getting-extensions }

Most Chrome users get extensions from the [Chrome Web Store][cws]. Developers
publish their extensions in the Chrome Web Store where they are reviewed and
made available to users.

{% Aside %}

Some organizations use [enterprise policies] to install extensions on their user's devices. These
extensions may either be fetched from the Chrome Web Store or hosted on the organization's web
servers.

{# TODO: See XXX for info on the capabilities and limitations of self hosting. #}

{% endAside %}

You can distribute your extensions through the [Chrome Developer Dashboard][dev-dashboard],
publishing them to the [Chrome Web Store][cws]. For more information, see the Chrome Web Store
[developer documentation][cws-docs].

### A note about extensions policy {: #policy }

Extensions on the Chrome Web Store must adhere to the [Chrome Web Store policies][cws-policies].
Here are some things to keep in mind as you begin:

* An extension must fulfill a [single purpose] that is narrowly defined and easy to understand. A
  single extension can include multiple components and a range of functionality, as long as
  everything contributes towards a common purpose.

{% Img src="image/SHhb2PDKzXTggPGAYpv8JgR81pX2/XniXB3snAeMvLwI1am3O.png", alt="Screenshot of AMP validator extension, pinned", width="169", height="62" %}

* User interfaces should be minimal and have intent. They can range from a simple icon (such as the
  [AMP validator][amp-validator] extension shown above) to opening a new window with a form (like the
  [Google Similar Pages][similar-pages-extension] extension shown below).

{% Img src="image/SHhb2PDKzXTggPGAYpv8JgR81pX2/oR9iCEgY2889Z3mHHLll.png", alt="Screenshot of Google
Similar Pages extension", width="334", height="597" %}

## Hello extensions {: #hello-extensions }

Take a small step into extensions with this quick Hello Extensions example. Start by creating a new
directory to store the extension's files, or download them from the [sample page][hello-sample].

Next, add a file called `manifest.json` and include the following code:

```json
{
  "name": "Hello Extensions",
  "description": "Base Level Extension",
  "version": "1.0",
  "manifest_version": 3
}
```

Every extension requires a manifest, though most extensions will not do much with just the manifest.
For this quick start, the extension has a popup file and icon declared under the
[`action`][action-field] field:

```json/5-8
{
  "name": "Hello Extensions",
  "description": "Base Level Extension",
  "version": "1.0",
  "manifest_version": 3,
  "action": {
    "default_popup": "hello.html",
    "default_icon": "hello_extensions.png"
  }
}
```

Download [`hello_extensions.png`][hello-uploader] and put it in the folder with your manifest. When you install the extension, the icon will be in Chrome toolbar and be used to start the extenion. This site's CDN gives the icon a hash for a name. You will need to rename it to match what's in the manifest file.

Next, create a file titled `hello.html`:

```html
<html>
  <body>
    <h1>Hello Extensions</h1>
  </body>
</html>
```

If this were installed, clicking the icon would display `hello.html` The next step is to include a
command in the `manifest.json` that enables a keyboard shortcut. This step is fun, but not
necessary:

```json/9-17
{
  "name": "Hello, Extensions",
  "description": "Base Level Extension",
  "version": "1.0",
  "manifest_version": 3,
  "action": {
    "default_popup": "hello.html",
    "default_icon": "hello_extensions.png"
  },
  "commands": {
    "_execute_action": {
      "suggested_key": {
        "default": "Ctrl+Shift+F",
        "mac": "MacCtrl+Shift+F"
      },
      "description": "Opens hello.html"
    }
  }
}
```

The last step is to install the extension on your machine.

1.  Navigate to `chrome://extensions` in your browser. You can also access this page by clicking on
    the Chrome menu on the top right side of the Omnibox, hovering over **More Tools** and selecting
    **Extensions**.
2.  Check the box next to **Developer Mode**.
3.  Click **Load Unpacked Extension** and select the directory for your "Hello, Extensions."


The extension icon should appear on your Chrome toolbar. (The toolbar is on the upper right of browser window, next to the address bar.) If you already have a lot of extensions installed, you'll need to deliberately pin the extension to the toolbar or it won't show up.

1. On the right end of the toolbar, click the black puzzle piece icon. A list of extensions appears.
2. In the list of extensions, scroll down until you find 'Hello, Extensions'.
3. Click the pin icon.

Congratulations! You can now use your popup-based extension by clicking the `hello_world.png` icon
or by pressing `Ctrl+Shift+F` on your keyboard.

## What next? {: #How-do-I-start }

1.  Follow the [Getting Started tutorial][getstarted-tut]
1.  Explore the [extension samples]
1.  Subscribe to the [chromium-extensions Google group][crx-group]

[amp-validator]: https://chrome.google.com/webstore/detail/amp-validator/nmoffdblmcmgeicmolmhobpoocbbmknc
[action-field]: /docs/extensions/reference/action
[crx-group]: http://groups.google.com/a/chromium.org/group/chromium-extensions
[cws]: https://chrome.google.com/webstore
[cws-docs]: /docs/webstore
[cws-policies]: /docs/webstore/program_policies/
[cws-publish]: /docs/webstore/publish/
[devmode]: /docs/extensions/mv3/getstarted/#manifest
[dev-dashboard]: https://chrome.google.com/webstore/devconsole
[enterprise policies]: https://cloud.google.com/docs/chrome-enterprise/policies/
[extension samples]: https://github.com/GoogleChrome/chrome-extensions-samples
[getstarted-tut]: /docs/extensions/mv3/getstarted
[hello-sample]: /docs/extensions/mv3/samples#search:hello
[hello-uploader]: https://storage.googleapis.com/web-dev-uploads/image/WlD8wC6g8khYWPJUsQceQkhXSlv1/gmKIT88Ha1z8VBMJFOOH.png
[similar-pages-extension]: https://chrome.google.com/webstore/detail/google-similar-pages/pjnfggphgdjblhfjaphkjhfpiiekbbej
[single purpose]: /docs/extensions/mv3/single_purpose
