---
layout: post
title:  "Generators in PHP 5.5 (with PDO)"
date:   2012-11-27 08:14:07
categories: php
---

As the first alpha of PHP 5.5 has been released, now is a great time to play with its new features. The first new feature is [generators](https://wiki.php.net/rfc/generators). Generators are like very simple iterators.

PDO’s PDOStatement class implements Traversable, which means you can iterate over it with foreach:

{% highlight php %}
<?php
$sth = $dbh->prepare("SELECT * FROM users");
$sth->execute();

foreach ($sth as $result) {
    print_r($result);
}
{% endhighlight %}

This is great as it abstracts away much of retrieving result sets from a database and gets you straight to an array-like structure. This iterator is great as PDO only retrieves the results as they’re requested, reducing memory usage.

You can recreate this by making a class that implements Iterator and its abstract methods. Whilst this is quite simple, PHP 5.5 brings generators, which allow you to create iterators using a single function.

Here’s an example, using PDO again. First, some boiler plate code to set up:

{% highlight php %}
<?php
$dbh = new PDO('sqlite::memory:'); 
$dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
$dbh->exec("CREATE TABLE users (
	id INTEGER NOT NULL,
	name VARCHAR(255),
	PRIMARY KEY (id)
)");

$dbh->exec("INSERT INTO users (id, name) VALUES (1, 'tom')");
$dbh->exec("INSERT INTO users (id, name) VALUES (2, 'dick')");
$dbh->exec("INSERT INTO users (id, name) VALUES (3, 'harry')");

$sth = $dbh->prepare("SELECT * FROM users");
$sth->execute();
{% endhighlight %}

If we wanted to, we could now iterate over $sth using foreach like so:

{% highlight php %}
<?php
foreach ($sth as $result) {
	print_r($result);
}
{% endhighlight %}

Instead, lets create a generator that fetches rows from $sth, but passes control back to the foreach:

{% highlight php %}
<?php
function fetch($sth, $fetchMode) {
    while ($result = $sth->fetch($fetchMode)) {
        yield $result;
    }
}
{% endhighlight %}

The key here is the “yield” keyword, which can be thought of as “start returning an item from an array”. The foreach can then loop through the results like this:

{% highlight php %}
<?php
$results = fetch($sth, PDO::FETCH_OBJ);
foreach ($results as $result) {
	echo $result->name . "\n";
}
{% endhighlight %}

Whilst this example is a bit pointless (as PDO already provides an Interator), you can see that generators are a simple way of creating iterators.

