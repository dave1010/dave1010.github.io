---
layout: post
title:  "Singletons: bad. Shared resources in a DIC: good."
date:   2014-12-15 07:44:07
categories: design php solid
---

## Singletons

The Singleton pattern is when you can only instantiate 1 instance of a class. Realistically the only way to do this in PHP is by controlling object lifetime inside the object itself globally. This is bad as it uses global state (and is why Singleton is often considered an anti pattern).

{% highlight php %}
<?php
trait Singleton {
    public static function getInstance()
    {
        static $instance = null;
        if (null === $instance) {
            $instance = new static();
        }
  
        return $instance;
    }
}
{% endhighlight %}

## Shared resources and services

Another object (eg a DI container) can decide to only return a single instance of a class if it wants to. It does this by creating it the first time (usually lazily, the first time it is created, rather than ASAP) then sharing that resource by storing it as state in the container.

{% highlight php %}
<?php
class Container {
    private $instances = [];
    private $factories = [];
    
    public function share($name, Callable $factory)
    {
        $this->factories[$name] = $factory
    }

    public function get($name)
    {
        if (!isset($this->instances[$name])) {
            $factory = $this->factories[$name];
            $this->instances[$name] = $factory;
        }

        return $this->instances[$name];
    }
}
{% endhighlight %}

## The similarities

Both patterns reduce the amount of instance creation. This reduces memory usage and time cost. This is especially useful when dealing with external resources (such as file and databases).

## The difference

Often people confuse the Singleton pattern with a container sharing a resource. You can pretty much guarantee that using a Singleton is a bad idea. Sharing resources, however, is often a good idea.

The difference between the Singleton pattern and a shared resource in a DIC is that the Singleton's state is global but the DIC's shared resource is stored in the DIC. The latter is not considered an anti pattern as the DIC is just like any object you can instantiate or mock.
