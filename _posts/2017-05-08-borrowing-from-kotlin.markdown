---
layout: post
title:  "Borrowing from Kotlin"
date:   2017-05-08 23:17:00 +0200
categories: posts
---

Not all projects can be easily migrated to Kotlin, but that doesn't mean we can't benefit from Kotlin within other languages.

<!--more-->

One of the things we can borrow from Kotlin in Java/Android is its clever naming scheme for constructing/converting objects.
Kotlin comes with various methods such as [mutableListOf()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-list-of.html):

```kotlin
val list = mutableListOf(1, 2, 3)
```

This allows Kotlin to define new static mapping/construction methods in a consistent manner.
Kotlin follows a naming scheme where `YourClass` becomes a static method `yourClassOf(...)`.

The benefit of using this in Java lies within your development environment. Once you type `yourClassOf`,
you can summon up all implementations that create an instance of `YourClass`. In Android Studio or IntelliJ, you just press `F1`:

![Android Studio / IntelliJ IDEA]({{ site.url }}/assets/images/posts/2017-05-08-ide.png)

So why not apply the same idiom to Java?