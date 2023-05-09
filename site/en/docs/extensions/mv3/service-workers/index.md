---
layout: 'layouts/doc-post.njk'
title: About extension service workers
description: Extension service workers are an extension's central event handler. That makes them different from web service workers. 
date: 2023-03-29
---

You should read this section whether you're familiar with service workers or not. Extension service workers are an extension's central event handler. That makes them just different enough from web service workers that the mountains of service worker articles around the web may or may not be useful. This section explains what you need to know to use service workers in extensions.

Extension service workers have a few things in common with their web counterparts. An extension service worker is loaded when it is needed, and unloaded when it goes dormant. Once loaded, an extension service worker generally runs as long as it is
actively receiving events, though it [can shut down](/docs/extensions/service_workers/lifecycle#idle-and-shutdown). Like its web counterpart, an extension service worker cannot access the DOM, though you can work around this with [offscreen documents](/docs/extensions/reference/offscreen/).

You shouldn't use extension service workers without knowing about their differences. That's what this section is about. Extension service workers are more than network proxies (as web service workers are often described). In addition to the [standard service worker events](https://developer.mozilla.org/docs/Web/API/ServiceWorkerGlobalScope#events), they also respond to extension events such as navigating to a new page, clicking a notification, or closing a tab. They're also registered and updated differently from extension service workers.

{% Aside %}
In some contexts here and around the web you will see service workers called "background scripts". Previously, this site often uses the terms interchangeably. Manifest V2 also included a feature called a background script. To avoid overloading the term and creating confusion, this section will use "extension service worker" or "service worker" throughout. 
{% endAside %}