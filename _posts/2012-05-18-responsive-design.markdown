---
layout: post
title:  "Taking Responsive Design a few steps forwards"
date:   2012-05-18 12:24:07
categories: css web
---

Responsive design today is great: different layouts can be served to different screen sizes. In the future, extra media queries could be available to web developers and designers to allow the content of a page respond to a whole range of capabilities.

Possibly the most useful is being able to serve different content based on network bandwidth and latency. If on a limited data plan, you could tell websites you don’t want want HD content by default. Mobile users with high bandwidth but a poor network latency may have content adjusted for them in a different way to someone connected via a low-latency, low-bandwidth ethernet link.

This would allow device pixel media queries to be used just for screen sizes, without making assumptions that would end up showing a full site to someone tethering their laptop to a GPRS connection or a cut-down version to someone who happened to resize their browser.

In addition to network conditions (and even preferences), a whole host of other capabilities could be determined via media queries. A hover menu could be limited to users with a mouse. Keyboard shortcuts could be shown to users with hardware keyboards. Complex animations could be disabled by default for users with lower-spec CPUs or GPUs.

This finer grained view of the user would reduce the assumptions currently made by lots of websites and allow website authors to provide sensible defaults to users without having to assume context.

In terms of getting these implemented, the next steps would probably be to have discussion on W3C/WHATWG mailing lists and file some bugs in browsers. Would anyone like to help take responsive design further?
