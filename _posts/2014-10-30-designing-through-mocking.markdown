---
layout: post
title:  "Notes on designing through mocking"
date:   2014-10-30 21:44:07
categories: design php
---

My notes on Everzet's talk "Design how your objects talk through mocking" at PHPNW14

* Everzet's [Original slide deck](http://www.slideshare.net/everzet/design-how-your-objects-talk-through-mocking)
* Code sample here are using [Prophecy](https://github.com/phpspec/prophecy) to create test doubles

# Different test doubles: Mocks, stubs, spies, dummies and fakes

## Dummy

* Don't care about. Passed around for typehinting.
* No behaviour

{% highlight php %}
<?php
class Foo { public function __construct(Dummy $dummy) {} }
new Foo((new \Prophecy\Prophet)->prophesize(Dumnmy::class)->reveal());
{% endhighlight %}

## Stub

* Has (basic) behaviour but no expectations (i.e. injects input into SUT but never handles output)
* Dummy that you call a method on
* Promises it will always return the same thing (`willReturn()`)
* Doesn't have to be called

{% highlight php %}
<?php
function baz(Stub $stub) { return $stub->foo(123) === 'bar';}
$stub = (new \Prophecy\Prophet)->prophesize(Stub::class);
$stub->foo(123)->wilLReturn('bar');
baz($stub->reveal());
{% endhighlight %}

## Mock

* Define predictions (`shouldBeCalled()`), not promises
* Verifies input from SUT

{% highlight php %}
<?php
function foo(Mock $mock) { $mock->bar(123); }
$prophet = new \Prophecy\Prophet;
$mock = $prophet->prophesize(Mock::class);
$mock->bar(123)->shouldBeCalled();
foo($mock->reveal());
$prophet->checkPredictions();
{% endhighlight %}

## Spy

* Records behaviour
* Assertions happen aftwerwards (`shouldHaveBeenCalled()`)

{% highlight php %}
<?php
function foo(Spy $spy) { $spy->bar(123); }
$spy = (new \Prophecy\Prophet)->prophesize(Spy::class);
foo($spy->reveal());
$spy->bar(123)->shouldHaveBeenCalled();
{% endhighlight %}

## Fake

* Return data depending on input
* Used to simplify a dependency. E.g. web service, DB, repository
* Used to isolate SUT

{% highlight php %}
<?php
interface WebServiceInterface { public function strtoupper($data); }
class RealWebService implements WebServiceInterface {
    public function strtoupper($data) { return file_get_contents('api.com/strtoupper/'.$data); }
}
class FakeWebService implements WebServiceInterface {
    public function strtoupper($data) { return strtoupper($data); }
}
{% endhighlight %}

# How mocking helps you follow SOLID principles

* lots of mocks means *SRP* violation. Don't fake parts of objects: decouple them
* Duplication in tests means *OCP* violation
* Refused bequest: when you call a method that you didn't expect and there's an interface that covers the method. Means drivers are incompatible.
* *LSP*: if objects implement the same interface then they should have the same behaviour. To fix, make a new interface and an adaptor
* *ISP* violation. Don't force objects to have methods that aren't in a test. If a test doesn't use a whole interface then split the interface
* *DIP*. Don't mock things you don't own. Don't test code you don't own, that's an integration test.

# Testing outcomes vs testing communication between objects

* Exposing outcomes vs exposing communications. Both. Tests usually expose outcomes but communication is important too.
* Exposing outcomes forces you to create meaningless getters just for tests
* Test outcomes when just 1 object. Test communication when multiple.
* Exposing communication creates more objects
* Don't use mocks for isolation, that's not what they're for (that's fakes)
* Messaging is more important than state

## Summary

* TDD based on communication fixes SOLID violations before they happen

