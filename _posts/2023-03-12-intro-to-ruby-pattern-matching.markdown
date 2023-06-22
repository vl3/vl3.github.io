---
layout: post
title:  "Intro to Ruby Pattern Matching"
date:   2023-03-05 02:03:34 -0300
categories: ruby pattern-matching
---

Pattern matching is a feature first introduced in Ruby 2.7 that since Ruby 3 it's no longer experimental.
Pattern matching allows you to match a value against a pattern or structure (could be primitives, a Ruby object or a Struct) and binding those matched values to local variables.

In other words, it offers a way to simplify complex conditional statements by using a declarative syntax that is easier to read and reason about.

Pattern Matching is implemented by using the case/in expression:

{% highlight ruby %}
case <expression>
in <pattern1>
  ...
in <pattern2>
  ...
in <pattern3>
  ...
else
  ...
end
{% endhighlight %}

So as an example, let's use a communication with a 3rd party system that will return 