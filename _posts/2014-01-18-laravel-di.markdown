---
layout: post
title:  "Better Dependency Injection in Laravel"
date:   2012-01-18 12:24:07
categories: php laravel
---

We all love using Dependency Injection but often Laravel’s facades and IoC container are used more like a static service locator, which (despite being testable) can easily lead to violations of the [Dependency Inversion Principle](http://en.wikipedia.org/wiki/Dependency_inversion_principle) (the D in [SOLID](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)).

There’s been a lot of discussion around this in the PHP community recently, with calls to [stop using facades](http://programmingarehard.com/2014/01/11/stop-using-facades.html) as well as how [service locators can be used safely](http://dongilbert.net/a-case-for-service-location/) (the comments here are good too).

The great news is Taylor, the main author of Laravel, has [responded to this](http://taylorotwell.com/response-dont-use-facades/) and [made a change](https://github.com/laravel/framework/commit/7e3a99ebbab959324eebd88bdc38667f3fd35461) to Laravel in v4.1.15 that makes it much easier to inject dependencies directly into controllers.

I’m happy to see constructive criticism actually influencing and benefiting a framework so positively. Hopefully we’ll see more if this in the PHP community!
