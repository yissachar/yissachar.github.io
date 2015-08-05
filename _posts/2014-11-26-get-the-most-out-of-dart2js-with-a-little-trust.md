---
layout: post
title: Get the Most Out of dart2js with a Little Trust
---

If you've ever looked at the JavaScript that dart2js outputs, you know there's a lot of extra type checking and other unnecessary code. But did you know that you could remove a lot of that extra code with the right dart2js flags?

[--trust-type-annotations](https://codereview.chromium.org/14969040/) and [--trust-primitives](https://codereview.chromium.org/703083002/) are two relatively unknown flags that tell dart2js to trust your code more, allowing it to better infer types and remove type checking, which results in smaller and more performant code.

### Trust Type Annotations
This flag lets dart2js trust that the types you provide are correct, which it can then use to prune out code that won't be reached. Let's take a look at an example:

{% highlight dart %}
void main() {
  Cat cat = getAnimal('cat');
  cat.greet();
}

getAnimal(String type) {
  if(type == 'cat') {
    return new Cat();
  } else if(type == 'dog') {
    return new Dog();
  }

  return null;
}

class Cat {
  greet() {
    print('Meow.');
  }
}

class Dog {
  greet() {
    print('Woof!');
  }
}
{% endhighlight %}

In this program, the greet method of `Dog` is never called. However, when we run dart2js we get the following code (along with all the surrounding boilerplate, but that isn't important here): 

{% highlight javascript %}
main: function() {
  F.getAnimal("cat").greet$0();
},
getAnimal: function(type) {
  if (type === "cat")
    return new F.Cat();
  else if (type === "dog")
    return new F.Dog();
  return;
},
Cat: {
  "^": "Object;",
  greet$0: function() {
    P.print("Meow.");
  }
},
Dog: {
  "^": "Object;",
  greet$0: function() {
    P.print("Woof!");
  }
}
{% endhighlight %}

Dartjs isn't able to remove `Dog.greet` because it sees that `getAnimal` can return a `Dog`. However, if it trusted our `Cat` type annotation, it would be able to infer that `Dog.greet` isn't needed. This is exactly what --trust-type-annotations does. Re-running dart2js with this flag enabled gives us the following JavaScript:

{% highlight javascript %}
main: function() {
  F.getAnimal("cat").toString;
  P.print("Meow.");
},
getAnimal: function(type) {
  if (type === "cat")
    return new F.Cat();
  else if (type === "dog")
    return new F.Dog();
  return;
},
Cat: {
  "^": "Object;"
},
Dog: {
  "^": "Object;"
}
{% endhighlight %}

Now dart2js was able to remove `Dog.greet` since it knew from our `Cat` type annotation that the method would never be called. Then `Cat.greet` was inlined as a standard optimization since it is only called from one location. 

This is a pretty nice improvement, but there's a reason this functionality is hidden behind a flag and not set as the default. If you had used the wrong type annotation for the result of `getAnimal`:

{% highlight dart %}
Dog cat = getAnimal('cat')
{% endhighlight %}

You would get the following JavaScript:

{% highlight javascript %}
main: function() {
  F.getAnimal("cat").toString;
  P.print("Woof!");
},
getAnimal: function(type) {
  if (type === "cat")
    return new F.Cat();
  else if (type === "dog")
    return new F.Dog();
  return;
},
Cat: {
  "^": "Object;"
},
Dog: {
  "^": "Object;"
}
{% endhighlight %}

Which incorrectly causes our cat to woof!

This is because dart2js assumes that our types are correct and performs the optimizations accordingly. Without the --trust-type-annotations flag, the JavaScript would still run correctly, despite the incorrect type annotation, since no type-based optimization would have been made. This means that if you want to use this flag, you need to be very confident that your type annotations are correct.

### Trust Primitives

The second flag tells dart2js to remove array bound checking and arithmetic type checking.

{% highlight dart %}
void main() {
  print(1 + getOne(true));
}

getOne(bool nonNull) {
  if(nonNull) {
    return 1;
  } else {
    return null;
  }
}
{% endhighlight %}

Without any flags this code produces the following JavaScript:

{% highlight javascript %}
main: function() {
  var t1, line;
  t1 = F.getOne(true);
  if (typeof t1 !== "number")
    return H.iae(t1);
  line = "" + (1 + t1);
  H.printString(line);
},
getOne: function(nonNull) {
  if (nonNull)
    return 1;
  else
    return;
}
{% endhighlight %}

Dart2js adds in a type check before performing the addition, increasing the size of the code and slowing down execution speed.

However if we add the --trust-primitives flag we can remove this behaviour:

{% highlight javascript %}
main: function() {
  var line = "" + (1 + F.getOne(true));
  H.printString(line);
},
getOne: function(nonNull) {
  if (nonNull)
    return 1;
  else
    return;
}
{% endhighlight %}

Similar checks are done on array bounds, and --trust-primitives remove those as well.

Trusting primitives can cause problems in some cases. The commit message for the flag warns:

>The model is that if your program is tested and never 
>
- has a bounds error on a primitive array 
- tries to do arithmetic on null

> then when compiled with --trust-primitive it will produce the same answer, but with smaller and faster code.

### Testing on a real project
Now that we've seen what these flags do, let's see how well they work on a real project. Joel Webber has a [Box2D benchmark](https://github.com/joelgwebber/bench2d) that suits our purposes.

By modifying the pubspec to the following we can tell dart2js to use the trust flags (since they haven't yet been explicitly added as options, we have to pass them through commandLineOptions):

{% highlight yaml %}
transformers:
- $dart2js:
    checked: false
    minify: true
    commandLineOptions: ['--trust-type-annotations', '--trust-primitives']
{% endhighlight %}

Note that the --trust-primitives flag is only available in SDK 1.8 and later, and will have no effect if you use it on an earlier version.

Now if we build the project dart2js will use our flags to generate more optimized JavaScript.

In the table below I've summarized the effect of each flag on code size and speed:

<table>
  <th></th>
  <th>Code Size (kb)</th>
  <th>Speed (ms)</th>
  <tr>
	<td>No flags</td>
    <td>296</td>
    <td>37</td>
  </tr>
  <tr>
    <td>Minify</td>
    <td>120</td>
    <td>37</td>
  </tr>
  <tr>
    <td>Minify + Trust Types</td>
    <td>115</td>
    <td>34</td>
  </tr>
  <tr>
    <td>Minify + Trust Primitives</td>
    <td>107</td>
    <td>36</td>
  </tr>
  <tr>
    <td>Minify + Trust Types + Trust Primitives</td>
    <td>103</td>
    <td>32</td>
  </tr>
</table>

You will get the most out of the trust flags if you use both together. For the Box2D benchmark, this meant an almost 14% improvement in both generated code size and execution speed, compared to the plain minified version. However, each project will have different levels of improvements, depending on their code patterns.

The --trust-type-annotations and --trust-primitives flags aren't suitable for every project, but they can provide a big win for app performance and generated code size if you conform to their rules.