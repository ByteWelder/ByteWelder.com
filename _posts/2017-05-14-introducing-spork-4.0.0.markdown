---
layout: post
title:  "Introducing Spork 4.0.0"
date:   2017-05-14 17:11:00 +0200
categories: posts
---

I've been working hard on [Spork][spork-website] 4.0.0 since last summer.

<!--more--> 

Spork is a [high performance][spork-performance]
runtime annotation processing framework with implementations for Android and for dependency injection.
It is intended as a replacement for [Butter Knife][butter-knife] and/or [Dagger 2][dagger], that's why
its functionality is heavily modelled onto the design of these libraries.

Let's take a look at the Android and dependency injection features...

## Spork for Android

Here's a code snippet of several Android bindings with the `spork-android` dependency:

```java
@BindLayout(R.layout.activity_download)
public class DownloadActivity extends Activity {

    @BindView(R.id.download_button)
    private Button downloadButton;

    @BindFragment(R.id.details_fragment)
    private DetailsFragment fragment;

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        Spork.bind(this); // bind() wires it all up!
    }

    @BindClick(R.id.other_button)
    private void onClickButton(Button someButton) {
        downloadManager.startDownload();
    }
}
```

By calling `Spork.bind()` in `onCreate()` the `View`, `OnClickListener` and the `Fragment` are
all automatically resolved without the need for all that boilerplate code.
This is all pretty much the same as with Spork 3.x

These are all the supported annotations:
- `@BindLayout`
- `@BindView`
- `@BindFragment`
- `@BindResource`
- `@BindClick`

## Spork Dependency Injection

The `spork-inject` library creates instances of your classes and satisfies their dependencies.
Let's first take a look at a regular constructor injection without `spork-inject`:

```java
class CoffeeMug {
    private Coffee coffee;
    private Mug mug;

    CoffeeMug(Coffee coffee, Mug mug) {
        this.coffee = coffee;
        this.mug = mug;
    }
}
```

Creating a `CoffeeMug` requires you to pass along its dependencies manually.
This is not a difficult task for a simple object with simple dependencies,
but it gets a lot more tedious with scopes and lifecycle considerations. Spork takes care of all that.

Spork can inject fields directly. In this example it obtains a `Coffee` and a `Mug` instance for the respective fields:

```java
class CoffeeMug {
    @Inject private Coffee coffee;
    @Inject private Mug mug;

    ...
}
```

Spork also supports method injection, but Field injection is generally preferred.

### Declaring Dependencies

In the above sample, a `Coffee` and `Mug` are injected. Of course these dependencies must come from somewhere.

Dependencies should be defined in a `Module`:

```java
@Provides
public Coffee provideCoffee() {
    return new BlackCoffee();
}
```

A `@Provides` method can require dependencies on its own. These are passed on as method arguments and
they are automatically resolved by Spork:

```java
@Provides
public Coffee provideCoffee(Water water, CoffeeBeans beans) {
    return new BlackCoffee(water, beans);
}
```

### Modules

The `@Provides`-annotated methods above are placed in a `Module`. Modules are POJO objects that define a set of dependencies:

```java
public class BrewModule {
    @Provides
    public Mug provideMug() {
        return new MugWithPrint("Input Java, output Java.");
    }

    @Provides
    public Water provideWater() {
        return new BoilingWater();
    }

    @Provides
    public Beans provideBeans() {
        return new ArabicaBeans();
    }

    @Provides
    public Coffee provideCoffee(Water water, CoffeeBeans beans) {
        return new BlackCoffee(water, beans);
    }
}
```

#### Building an ObjectGraph

One or more modules are used to build an object graph. Object graphs hold state such as your singletons and named instances.

Creating an `ObjectGraph` is easy:

```java
ObjectGraph objectGraph = ObjectGraphs.builder()
    .module(new BrewModule())
    .build();
```

When putting it all together, the `CoffeeMug` can now be injected with an `ObjectGraph` made with the `BrewModule`:

```java
class CoffeeMug {
    @Inject private Coffee coffee;
    @Inject private Mug mug;

    public CoffeeMug() {
        ObjectGraphs.builder()
            .module(new BrewModule())
            .build()
            .inject(this); // same as calling Spork.bind(this, objectGraph)
    }
}
```

### Scoped injection

A scoped instance is an instance that belongs to a specific `ObjectGraph` created at a specific level of the application. It is tied to the lifecycle of that `ObjectGraph`.

`@Singleton` is a predefined scope that is always available at the root `ObjectGraph` in your application. It is tied to the lifecycle of that `ObjectGraph`.

`@Provides` methods in a module can specify a scope. It can be used like this:

```java
@Provides
@Singleton
public CoffeeService provideCoffeeService() {
    return new CoffeeServiceImpl();
}
```

Scopes can also be made custom.

Check out the full [spork-inject User Guide][spork-userguide] for more details.

### Qualifiers

In some cases, you might want to identify an injection by some kind of identifier. This is done with a qualifier.

The `@Named` annotation is a qualifier that is available by default. It can be used in a module:

```java
class WaterModule {
    @Provides
    @Named("cold")
    public Water provideColdWater() {
        ...
    }

    @Provides
    @Named("hot")
    public Water provideHotWater() {
        ...
    }
}
```

`WaterModule` can then be used to inject a `Faucet` class with the same annotation:

```java
class Faucet {
    @Inject @Named("cold") Water coldWater;

    @Inject @Named("hot") Water hotWater;

    ...
}
```

You can even create your own qualifier annotations.

## Dependencies

All dependencies are hosted on jcenter, which is the default repository when developing Android projects.

To use dependency injection:

```groovy
dependencies {
    compile 'com.bytewelder.spork:spork-inject:4.0.0'
}
```

To use Android bindings:
```groovy
dependencies {
    compile 'com.bytewelder.spork:spork-android:4.0.0@aar'
}
```

To use Android bindings with AppCompat/Support:
```groovy
dependencies {
    compile 'com.bytewelder.spork:spork-android-support:4.0.0@aar' {
        exclude group: 'com.android.support'
    }
}
```

## Closing words

Check out the [Spork website][spork-website] or the [documentation][spork-docs] for more information.

Please file bug reports and feature requests at [Spork's GitHub repo][spork-github].

[spork-website]: http://spork.bytewelder.com
[spork-docs]: http://spork.bytewelder.com/docs/4.0/
[spork-github]: https://github.com/ByteWelder/Spork
[spork-userguide]: http://spork.bytewelder.com/docs/4.0/Dependency_Injection/User_Guide.html
[spork-performance]: http://spork.bytewelder.com/docs/4.0/About/Performance/index.html
[butter-knife]: https://jakewharton.github.io/butterknife/
[dagger]: https://google.github.io/dagger/