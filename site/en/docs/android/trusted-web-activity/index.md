---
layout: "layouts/doc-post.njk"
title: Overview
seoTitle: Android Trusted Web Activities overview
date: 2020-02-04
updated: 2020-07-31
description: Learn how you can seamlessly integrate your Progressive Web App into your Android App with a Trusted Web Activity.
authors:
  - petelepage
  - andreban
---

{% YouTube
  id='6lHBw3F4cWs'
%}

**Trusted Web Activity** is a new way to open _your_ web-app content
such as _your_ Progressive Web App (PWA) from _your_ Android app using a protocol based on Custom
Tabs.

Note: Trusted Web Activity is available in [Chrome on Android][6], version 72 and above.

_Looking for the code?_

* [android-browser-helper library on GitHub][9]
* [Trusted Web Activity demos][10]
* [Bubblewrap, a NodeJs library / CLI to generate and build Trusted Web Activity projects][11]

There are a few things that make Trusted Web Activity different from other
ways to open web content from your Android app:

1. Content in a Trusted Web activity is **trusted** -- the app and the site it
   opens are expected to come from the same developer. (This is verified using
   [Digital Asset Links][12].)
1. The content rendered in a Trusted Web Activity comes from the **web**: they're
   rendered by the user's browser, in exactly the same way as a user would see
   it in their browser except they are run fullscreen. Web content should be
   accessible and useful in the browser first.
1. Browsers are also updated independent of Android and your app -- Chrome, for
   example, is available back to Android Jelly Bean. That saves on APK size and
   ensures you can use a modern web runtime. (Note that since Lollipop, WebView
   has also been updated independent of Android, but there are a [significant
   number](https://developer.android.com/about/dashboards/index.html) of
   pre-Lollipop Android users.)
1. The host app doesn't have direct access to web content in a Trusted Web
   Activity or any other kind of web state, like cookies and `localStorage`.
   Nevertheless, you can coordinate with the web content by passing data to and
   from the page in URLs (e.g. through query parameters and 
   [intent URIs](/docs/multidevice/android/intents).)
1. Transitions between web and native content are between **activities**. Each
   activity (i.e. screen) of your app is either completely provided by the web,
   or by an Android activity

To make it easier to test, there are currently no qualifications for content
opened in the preview of Trusted Web activities. You can expect, however, that
Trusted Web activities will need to meet the same
[Add to Home Screen](https://web.dev/customize-install/#criteria)
requirements. You can audit your site for these requirements using the
[Lighthouse][13] "*user can be prompted to Add to Home
screen*" audit.

Today, if the user's version of Chrome doesn't support Trusted Web activities,
Chrome will fall back to a simple toolbar using a Custom Tab. It
is also possible for other browsers to implement the same protocol that Trusted
Web activities use. While the host app has the final say on what browser gets
opened, we recommend the same policy as for Custom Tabs: use the user's default
browser, so long as that browser provides the required capabilities.

## Where to go next

If you are looking for quickly building an Android app that just starts and opens your PWA,
checkout out the [Quick Start Guide][7].

If integrating Trusted Web Activity into an existing Android App, the [Integration Guide][8]
is a good place to get started.

[6]: https://play.google.com/store/apps/details?id=com.android.chrome
[7]: /docs/android/trusted-web-activity/quick-start/
[8]: /docs/android/trusted-web-activity/integration-guide/
[9]: https://github.com/GoogleChrome/android-browser-helper
[10]: https://github.com/GoogleChrome/android-browser-helper/tree/master/demos
[11]: https://github.com/GoogleChromeLabs/bubblewrap
[12]: https://developers.google.com/digital-asset-links/v1/getting-started
[13]: https://web.dev/measure/
