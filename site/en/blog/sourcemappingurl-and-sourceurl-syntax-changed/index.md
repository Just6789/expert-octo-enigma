---
layout: "layouts/blog-post.njk"
title: sourceMappingURL and sourceURL syntax changed
description: >
  sourceMappingURL and sourceURL syntax changed
authors:
  - paulirish
date: 2013-06-12
updated: 2019-03-09
---

If you use either source maps or sourceURL (both covered in the [HTML5 Rocks Primer on Sourcemaps](https://www.html5rocks.com/en/tutorials/developertools/sourcemaps/)), then you may see a warning in Chrome console like `"/*@ sourceMappingURL=" source mapping URL declaration is deprecated, "/*# sourceMappingURL=" declaration should be used instead.`

<figure>
{% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/xoBTCsMxY6TknjLbS2P4.png", alt="Sourcemapping Devtools screenshot", width="702", height="101" %}
</figure>


Here's what that's about:

## Impetus
`//@ sourceMappingURL` [was found](https://bugs.jquery.com/ticket/13274) to have a conflict with IE whenever it was
found in the page after `//@cc_on` was interpreted to turn on conditional
compilation in the IE JScript engine. A legacy version of the HTML5 Shiv is one
particular offender here.

## Spec Change
The `//@ sourceMappingURL` syntax is defined in the [Sourcemap V3 spec](https://docs.google.com/document/d/1U1RGAehQwRypUTovF1KRlpiOFze0b-_2gc6fAH0KY0k/edit#heading=h.lmz475t4mvbx)
It was changed there to use `//#` syntax instead.

## sourceURL
`//@ sourceURL` is also defined in the spec and was made to match the `//#` syntax
for consistency. Follow through, for details on
[what sourceURL does](https://www.html5rocks.com/tutorials/developertools/sourcemaps/#toc-sourceurl). It's used by Ember's [minispade](https://github.com/wycats/minispade), Google's [concatenate.js](https://github.com/google/concatenate.js), and others. In Chrome, `sourceURL` is supported for inline scripts and inline styles, in addition to evaluated JS.

## Implementation in Browser DevTools = done!

* **Safari Inspector** now supports `//#` for sourceMappingURL and sourceURL
* **Firebug's** change [has
  landed](https://github.com/firebug/firebug/commit/f14828954c4f9e07e8b86f9317713e774c9ad5d5)
  for sourceURL.
* **Firefox** [landed](https://bugzilla.mozilla.org/show_bug.cgi?id=870361) the change for
  sourceMappingURL. The sourceURL [ticket is
  here](https://bugzilla.mozilla.org/show_bug.cgi?id=833744).
* **Chrome** **DevTools** [landed the
  change](https://codereview.chromium.org/15832007) for sourceMappingURL and
  sourceURL. It will also warn about use of the deprecated `//@` syntax.

While these changes make their way to stable release, you can use both syntaxes simultaneously for full tool support or migrate immediately to the `#` syntax, depending on your needs.

