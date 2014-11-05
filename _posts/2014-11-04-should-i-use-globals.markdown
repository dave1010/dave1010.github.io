---
layout: post
title:  "Should I use global variables?"
date:   2014-11-04 22:44:07
categories: design php solid
---

Someone [posted a question](http://www.reddit.com/r/PHP/comments/2lct7y/question_do_you_use_globals_or_not/) about using global variables on [r/php](http://www.reddit.com/r/PHP), which I answered. Copied here for posterity.

Using global state is technical debt. This means you're trading off short term wins (mostly writing less boiler plate code) with long term losses. If you get good at writing clean, SOLID code then any short term win from a shortcut is pretty much removed. There are lots of things that influence whether taking on technical debt is a good idea or not (including non-technical reasons), so the only correct answer is "it depends". 

In my experience: **taking on this form of technical debt is virtually never worth it**.

With a global variable you're doing something like this:

{% highlight php %}
class Foo {
    public function __construct()
    {
        $this->db = $GLOBALS['db'];
    }
}
$foo = new Foo;
{% endhighlight %}

In this case, `$foo` has a hidden dependency on `$GLOBALS['db']`. That means from the outside, Foo is much harder to use, test, refactor and extend. Without refactoring, your code will rot whenever you add new features.

Injecting your dependencies into `Foo` gives you the ability to decouple `Foo` from its dependencies. This makes testing and refactoring much easier. This can be done in 1 of 2 ways. Injecting a service locator is the first way to decoupling `Foo`.

{% highlight php %}
class Foo {
    public function __construct(ServiceLocator $serviceLocator)
    {
        $this->db = $serviceLocator->getDb();
    }
}
new Foo($serviceLocator);
{% endhighlight %}

A service locator gives you separation between `Foo`'s dependency on the DB abstraction and the actual DB implementation. The service locator still keeps the dependency hidden inside `Foo` though, which means `Foo` is still quite hard to refactor. In fact, if your `$serviceLocator` itself is in the global scope, it's almost the same as injecting `$GLOBALS` into `Foo` (which isn't great):

{% highlight php %}
class Foo {
    public function __construct(array &$globals)
    {
        $this->db = $globals['db'];
    }
}
new Foo($GLOBALS);
{% endhighlight %}

The best thing to do is to inject the actual dependencies into `Foo`. This means you can follow the [Dependency Inversion Principle](http://en.wikipedia.org/wiki/Dependency_inversion_principle) (the D in [SOLID](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)).

{% highlight php %}
class Foo {
    public function __construct(Connection $db)
    {
        $this->db = $db;
    }
}
new Foo($db);
{% endhighlight %}

If by doing this you're thinking "hey, that means I have to inject $db into hundreds of places" then your code is already relying on dependencies all over the places. This means you should (to speed up development and improve quality in the long term) refactor classes that depend on the dependencies to define clear responsibilities (the [S in SOLID](http://en.wikipedia.org/wiki/Single_responsibility_principle)).

Note: as with all cases with technical debt vs clean code, there is a trade off. In general, your future self will thank you for doing it correctly early on.
