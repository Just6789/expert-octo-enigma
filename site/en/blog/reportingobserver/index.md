---
layout: 'layouts/blog-post.njk'
title: 'ReportingObserver: know your code health'
date: 2018-07-26
updated: 2019-09-28
authors:
  - philipwalton
description: ReportingObserver gives developers insight into what their code is doing in the wild. ReportingObserver surfaces information on issues like deprecations and interventions, messages that were previously only available in the DevTools console.
---

## TL;DR
There's a new observer in town! `ReportingObserver` is a new API that lets you
know when your site uses a deprecated API or runs into a
[browser intervention][interventions]:

```js
const observer = new ReportingObserver(
  (reports, observer) => {
    for (const report of reports) {
      console.log(report.type, report.url, report.body);
    }
  },
  {buffered: true}
);

observer.observe();
```

The callback can be used to send reports to a backend or analytics provider
for further analysis.

Why is that useful? Until now, deprecation and
intervention warnings were only available in the DevTools as console messages.
Interventions, in particular, are only triggered by various real-world constraints
like device and network conditions. Thus, you may never even see these messages
when developing/testing a site locally. `ReportingObserver` provides
the solution to this problem. When users experience potential issues in the wild,
we can be notified about them.

{% Aside %}
`ReportingObserver` has only shipped in Chrome 69. It is being considered by
other browsers.
{% endAside %}


## Introduction 

A while back, I wrote a blog post ("[Observing your web app](https://ericbidelman.tumblr.com/post/149032341876/observing-your-web-app)")
because I found it fascinating how many APIs there are for monitoring the
"stuff" that happens in a web app. For example, there are APIs that can observe
information about the DOM: `ResizeObserver`,
`IntersectionObserver`, `MutationObserver`. There are APIs for capturing
performance measurements: `PerformanceObserver`. Other
APIs like `window.onerror` and `window.onunhandledrejection` even let us know
when something goes wrong.

However, there are other types of warnings which are not captured by these
existing APIs. When your site uses a deprecated API or runs up
against a [browser intervention][interventions], DevTools is first to tell you
about them:

<figure>
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/3h7FuMOEgDifWS2gtyij.png", alt="DevTools console warnings for deprecations and interventions.", width="800", height="79" %}
  <figcaption>Browser-initiated warnings in the DevTools console.</figcaption>
</figure>

One would naturally think `window.onerror` captures these warnings. It does not!
That's because `window.onerror` does not fire for warnings
generated directly by the user agent itself. It fires for runtime errors
(JS exceptions and syntax errors) caused by executing your code.

`ReportingObserver` picks up the slack. It provides a programmatic way to be
notified about browser-issued warnings such as [deprecations][deprecations]
and [interventions][interventions]. You can use it as a reporting tool and
lose less sleep wondering if users are hitting unexpected issues on your live
site.

{% Aside 'key-term' %}
`ReportingObserver` is part of a larger spec, the [Reporting API](https://developers.google.com/web/updates/2018/09/reportingapi),
which provides a common way to send these different reports to a backend.
The Reporting API is basically a generic framework to specify a set of server
endpoints to report issues to.
{% endAside %}

## The API

The API is not unlike the other "observer" APIs such
as `IntersectionObserver` and `ResizeObserver`. You give it a callback;
it gives you information. The information that the callback receives is a
list of issues that the page caused:

```js
const observer = new ReportingObserver((reports, observer) => {
  for (const report of reports) {
    // → report.type === 'deprecation'
    // → report.url === 'https://reporting-observer-api-demo.glitch.me'
    // → report.body.id === 'XMLHttpRequestSynchronousInNonWorkerOutsideBeforeUnload'
    // → report.body.message === 'Synchronous XMLHttpRequest is deprecated...'
    // → report.body.lineNumber === 11
    // → report.body.columnNumber === 22
    // → report.body.sourceFile === 'https://reporting-observer-api-demo.glitch.me'
    // → report.body.anticipatedRemoval === <JS_DATE_STR> or null
  }
});

observer.observe();
```

### Filtered reports

Reports can be pre-filter to only observe certain report types:

```js
const observer = new ReportingObserver((reports, observer) => {
  ...
}, {types: ['deprecation']});
```

{% Aside 'key-term' %}
Right now, there are two report types: `'deprecation'` and `'intervention'`.
{% endAside %}

#### Buffered reports 

The `buffered: true` option is really useful when you want to see the
reports that were generated before the observer was created:

```js
const observer = new ReportingObserver((reports, observer) => {
  ...
}, {types: ['intervention'], buffered: true});
```

It is great for situations like lazy-loading a library that uses
a `ReportingObserver`. The observer gets added late, but you
**don't miss out on anything that happened earlier in the page load**.

#### Stop observing

Yep! It's got a `disconnect` method:

```js
observer.disconnect(); // Stop the observer from collecting reports.
```

## Examples

**Example** - report browser interventions to an analytics provider:

```js
const observer = new ReportingObserver(
  (reports, observer) => {
    for (const report of reports) {
      sendReportToAnalytics(JSON.stringify(report.body));
    }
  },
  {types: ['intervention'], buffered: true}
);

observer.observe();
```

**Example** - be notified when APIs are going to be removed:

```js
const observer = new ReportingObserver((reports, observer) => {
  for (const report of reports) {
    if (report.type === 'deprecation') {
      sendToBackend(`Using a deprecated API in ${report.body.sourceFile} which will be
                     removed on ${report.body.anticipatedRemoval}. Info: ${report.body.message}`);
    }
  }
});

observer.observe();
```

## Conclusion

`ReportingObserver` gives you an additional way for discovering and monitoring
potential issues in your web app. It's even a useful tool for understanding the
health of your code base (or lack thereof). Send reports to a backend,
know about the real-world issues users are hitting on your site, update
code, profit!

### Future work 

In the future, my hope is that `ReportingObserver` becomes the de-facto API
for catching all types of issues in JS. Imagine one API to catch everything
that goes wrong in your app:

- [Browser interventions][interventions]
- Deprecations
- [Feature Policy][featurepolicy] violations. See [crbug.com/867471](https://bugs.chromium.org/p/chromium/issues/detail?id=867471).
- JS exceptions and errors (currently serviced by `window.onerror`).
- Unhandled JS promise rejections (currently serviced by `window.onunhandledrejection`)

I'm also excited about tools integrating `ReportingObserver` into
their workflows. [Lighthouse](/web/tools/lighthouse/) is an example of a tool
that already flags browser deprecations when you run its
"[Avoids deprecated APIs](/web/tools/lighthouse/audits/deprecated-apis)" audit:

<figure>
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/Cbsv6jhF91GxtTnFV1u0.png", alt="The Lighthouse audit for using deprecated APIs could use ReportingObserver.", width="800", height="249" %}
  <figcaption>The Lighthouse audit for using deprecated APIs could use ReportingObserver.</figcaption>
</figure>

Lighthouse currently uses the [DevTools protocol](https://chromedevtools.github.io/devtools-protocol/)
to scrape console messages and report these issues to developers. Instead, it
might be interesting to [switch to `ReportingObserver`](https://github.com/GoogleChrome/lighthouse/issues/5707)
for its well structured deprecation reports and additional metadata like
`anticipatedRemoval` date.

**Additional resources**:

- [W3c spec][reportingobserver]
- [chromestatus.com entry][chromestatus]

[spec]: https://w3c.github.io/reporting
[reportingobserver]: https://w3c.github.io/reporting/#observers
[explainer]: https://github.com/W3C/reporting/blob/main/EXPLAINER.md
[chromestatus]: https://www.chromestatus.com/feature/4691191559880704
[featurepolicy]: /web/updates/2018/06/feature-policy
[interventions]: https://www.chromestatus.com/features#intervention
[deprecations]: https://www.chromestatus.com/features#intervention
