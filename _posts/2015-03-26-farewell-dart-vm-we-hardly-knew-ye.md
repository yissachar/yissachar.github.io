---
layout: post
title: Farewell Dart VM, We Hardly Knew Ye
---

Today the hopes of ever seeing the Dart VM in Chrome [finally died](http://news.dartlang.org/2015/03/dart-for-entire-web.html).


I doubt this comes as much of a surprise to anyone who's been working with Dart these past few years. The Dart VM was never going to be added to any of the other browsers, so adding it to Chrome was a bit of a red herring.


### What changes now?
Technically nothing. There was never a Dart VM in a production browser, so Dart developers aren't losing anything. The same web app that worked yesterday will work tomorrow, compiling down to JS as usual.

However, there will be a perception hit for Dart. There is no shortage of [languages that compile to JavaScript](https://github.com/jashkenas/coffeescript/wiki/List-of-languages-that-compile-to-JS). What made Dart unique was that it also planned on adding a new VM to browsers. From day one this was the message that was communicated, and it's what got a lot of people interested in Dart. But this is no longer the case, and developers may now just shrug off Dart as "just another language that compiles to JS". Which is a shame, because Dart is about so much more than its VM.

### It's not about the VM
For me Dart is about the language, the tools, and the libraries. No other solution that I've tried has hit the sweet spot of productivity and enjoyment that Dart offers. Dart is a sane modern language that comes batteries-included, which makes it easy to get up and running, and the safety provided by the type system and the static analyzer mean that you can scale up your project without worry.

### There was that little performance thing though...
The Dart VM did offer the hope of increased web app performance and now that's gone. This is unfortunate for those developers that needed the extra horsepower. However, JavaScript engines are constantly improving, and new initiatives such as [SoundScript](https://developers.google.com/v8/experiments) promise greater performance by making JavaScript more Dart-like.

### The way forward
Though it may not initially seem like it, this announcement is a good thing for Dart. Instead of relying on the illusion of there one day being a browser based Dart VM that will save us all from JavaScript, we have to face reality. Dart's only way into the browser is through JavaScript, and therefore dart2js and JavaScript interop need to step up their game in order for Dart to properly compete with alternatives such as TypeScript. Instead of Dart constantly being surrounded by the question mark that was the Chrome VM, it's now clear where Dart stands, and developers can  make their decisions fully informed.

For some, the lack of a browser VM will be a deal-breaker, but I think that for the vast majority of developers Dart was never about the VM anyway, but rather using an awesome language to create great webapps. 

And that hasn't changed one bit.