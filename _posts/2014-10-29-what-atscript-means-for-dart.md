---
layout: post
title: What AtScript Means for Dart
---

Recently the Angular team announced [AtScript](https://docs.google.com/document/d/11YUzC-1d0V1-Q3V0fQ7KSit97HnZoKVygDxpWzEYW0U/preview?sle=true&pli=1), a new language that will compile down to JavaScript. AtScript aims to add features that will make large-scale application development easier.

Given the similar goals to Dart, the similar syntax, and the fact that both are developed primarily by Google employees this has led to questions about what this announcement means for Dart's future. Below, I will address some of the common questions that have come up.

Disclaimer: I am not a Google employee, nor do I work on the Dart or AtScript projects. These are my own views on the subject.   

### What does AtScript mean for Dart?
Nothing. No, really - nothing. All the benefits of using Dart still exist: tooling, optional typing, lexical scoping etc. In fact, Dart will only keep getting better.

### If Dart's so great why did Angular develop AtScript?
Angular could have used Dart, but Dart is currently weak in the area of JavaScript interop (though work on improving it is [underway](https://github.com/dart-lang/js-interop)), which was important to the Angular team.

### Then shouldn't I use AtScript instead of Dart?
If you really need tight JavaScript interop, maybe. AtScript, like [TypeScript](http://www.typescriptlang.org/) (which it is almost a superset of), aims for backward compatibility with JavaScript, which allows for tighter integration with existing code, but also keeps weird JavaScript quirks. Since Dart breaks with JavaScript, it is free to clean up the semantics. If you decided on Dart instead of TypeScript, AtScript probably shouldn't change your mind - it offers only incremental improvements over TypeScript.

### But if Google is developing AtScript, doesn't that mean they're dropping Dart?
Not at all. "Google" is not developing AtScript, the Angular team within Google is. Similarly, the Dart team develops Dart. There are multiple different teams within Google and they are free to choose what technology to use (Dart, [GWT](http://www.gwtproject.org/), TypeScript, Closure, etc.) or to develop their own if needed. The Angular team decided they needed a new language, while the Dart team is committed to improving Dart.

### What about AngularDart?
AngularDart will still be supported. In fact, AtScript will allow the Angular team to maintain a single code base that compiles down to both JavaScript and Dart.

### Final Note 
It is interesting to compare the reaction to AtScript with the reaction to Dart from GWT users when Dart was first announced. GWT users were also scared that Dart meant the end of the road for GWT. As it turns out, that was far from the case. Some of the most interesting GWT work has been done post-Dart, the GWT-SDK boasts ~150,000 active users, and the new Google Inbox webapp uses GWT.

The web is large enough to support multiple different approaches - choose the one that makes you most productive.