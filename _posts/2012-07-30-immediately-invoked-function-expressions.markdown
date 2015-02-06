---
layout: post
title:  "Immediately-Invoked Function Expressions in PHP"
date:   2012-07-30 08:14:07
categories: php javascript
---


### In JavaScript

A common patten in JavaScript is [immediately-invoked function expressions](http://benalman.com/news/2010/11/immediately-invoked-function-expression/), also known as self-executing anonymous functions. These allow you to create a private scope that won’t pollute the global namespace. They look like this:

{% highlight javascript %}
<?php
(function () {
    // variables defined here are 
    // only visible in this scope
}());
{% endhighlight %}

### In PHP

Since PHP 5.3 brought along anonymous functions, the same can be done in PHP. Albeit with a slightly less elegant syntax:

{% highlight javascript %}
<?php
call_user_func(function() {
    // do something
});
{% endhighlight %}


This is useful in PHP if you have some code that has side effects but you don’t want to litter variables.
Real world examples

When bootstrapping a framework or CMS, you often have to setup the environment by defining some constants. With IIFEs you’re free to use variables liberally so you’re code’s easier to understand.

{% highlight javascript %}
<?php
call_user_func(function() {
    // do some work, creating $result
    define('SOMETHING', $result)
});
{% endhighlight %}

You can even return a variable from the function and assign it to a variable:

{% highlight javascript %}
<?php
$var = call_user_func(function() {
	$a = 2;
	$b = 2;
	return $a + $b;
});
{% endhighlight %}

Another use is registering namespaces with autoloaders (if you’re not using composer yet):

{% highlight javascript %}
<?php
call_user_func(function() {
    require_once './vendor/symfony/src/Symfony/Component/ClassLoader/UniversalClassLoader.php';
    $loader = new Symfony\Component\ClassLoader\UniversalClassLoader();
    $loader->registerNamespace('Acme', __DIR__.'/vendor/acme/src');
});
{% endhighlight %}

### The future?

PHP 5.5 looks like it has lots of [interesting features](http://nikic.github.com/2012/07/10/What-PHP-5-5-might-look-like.html). Hopefully one them will be better function dereferencing. If it is, we can expect to have a syntax like the following, without the ugly call to call_user_func:

{% highlight javascript %}
<?php
// php 5.5?
function() {
    // do something
}();
{% endhighlight %}

It’s great to see PHP improving and gradually making functions first-class citizens. Perhaps one day we’ll be able to pass functions around in PHP as easily as as we can in languages like JavaScript.
