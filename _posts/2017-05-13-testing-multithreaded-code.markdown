---
layout: post
title:  "Testing multithreaded code"
date:   2017-05-13 13:54:00 +0200
categories: posts
---

Writing multi-threaded code for Java/Android is not the easiest of tasks. Making it testable can also be a challenge.

<!--more-->

In this post I will share with you a way to make multi-threaded code more testable.
Let's first take a look at an implementation of a cache that can store named instances, including a `null` value... and let's for the (in)convenience forget about `ConcurrentHashMap`...

```java
// Create a cache and a color factory
Cache cache = new Cache();
Factory colorFactory = new Factory();

// Cache creates and returns a white object
Object whiteObject = cache.getOrCreate("white", colorFactory);

// Cache creates and returns a yellow object
Object yellowObject = cache.getOrCreate("yellow", colorFactory);

// Cache returns the same white object from before
Object whiteObject2 = cache.getOrCreate("white", colorFactory);
```

This is the implementation of `Cache`:

```java
public class Cache {
    private final Map<String, Object> map = new HashMap<String, Object>();

    public Cache(Map<String, Object> map) {
        this.map = map;
    }

    public Cache() {
        this(new HashMap<String, Object>());
    }

    public Object getOrCreate(String name, Factory factory) {
        synchronized(map) {
            if (map.containsKey(name)) {
                return map.get(name);
            } else {
                Object instance = factory.create(name);
                map.put(name, instance);
                return instance;
            }
        }
    }
}
```

And this is how the `Factory` looks like:

```java
public interface Factory {
    Object create(String name);
}
```

Now we can start testing whether `getOrCreate()` creates the cache properly.

The following code uses [Mockito][mockito] for mocking and [Hamcrest][hamcrest] matchers for assertions:

```java
// given
HashMap<String, Object> map = spy(new HashMap<String, Object>());
Cache cache = new Cache(map);
Factory factory = mock(Factory.class);

// when getting an object with the same key twice in a row
cache.getOrCreate("something", factory);
cache.getOrCreate("something", factory);

// then verify that the factory is called only once
verify(factory).create("something");
verifyNoMoreInteractions(factory);
// and ensure the cache only has one item
assertThat(map.size(), is(1));
```

But how do you test the correct usage of the `synchronized` block? You don't!
This is where `Lock` comes to play. Let's refactor the example to use a `Lock`:

```java
public class Cache {
    private final Lock mapLock;
    private final Map<String, Object> map;

    public Cache(Map<String, Object> map, Lock mapLock) {
        this.map = map;
        this.mapLock = mapLock;
    }

    public Cache() {
        this(new HashMap<String, Object>(), new ReentrantLock());
    }

    public Object getOrCreate(String name, Factory factory) {
        mapLock.lock();

        try {
            if (map.containsKey(name)) {
                return map.get(name);
            } else {
                Object instance = factory.create(name);
                map.put(name, instance);
                return instance;
            }
        } finally {
            mapLock.unlock();
        }
    }
}
```

Now we can test whether our locking mechanism is used correctly:

```java
// given
HashMap<String, Object> map = spy(new HashMap<String, Object>());
Lock mapLock = spy(new ReentrantLock());
Cache cache = new Cache(map, mapLock);
Factory factory = mock(Factory.class);
String name = "something";

// when getOrCreate() is called, ignoring the result
cache.getOrCreate(name, factory);

// verify that the following methods are called in order
InOrder inOrder = inOrder(map, mapLock);
inOrder.verify(mapLock).lock();
inOrder.verify(map).containsKey(name);
inOrder.verify(map).put(name, null);
inOrder.verify(mapLock).unlock();
inOrder.verifyNoMoreInteractions();
```

We can also verify the expected behavior when an `Exception` is thrown:

```java
// given
HashMap<String, Object> map = spy(new HashMap<String, Object>());
Cache cache = new Cache(map, mapLock);
Factory factory = mock(Factory.class);

// given that creating a new item throws an exception
when(factory.create(any(String.class))).thenThrow(new RuntimeException("test"));

// when we create a new item for our cache
try {
    cache.getOrCreate("something", factory);
} catch (ObjectGraphException caught) {
    // catch, but do nothing
}

// verify that there was locking and unlocking in the correct order
InOrder inOrder = inOrder(mapLock);
inOrder.verify(mapLock).lock();
inOrder.verify(mapLock).unlock();
inOrder.verifyNoMoreInteractions();
// and assure nothing is stored in our map since the operation failed
assertThat(map.isEmpty(), is(true));
```

As you can see, there is no need to start threads to test correct usage of locks.
We don't really need to know whether the `Lock` implementation works: We just want to
see if our code is using it correctly in case the code is changed in the future.

This approach keeps your tests simple, readable and lightning fast to execute.

This does not cover everything there regarding [synchronization][java_synchronization], but it's at least a way to improve the testability of your code.

[mockito]: http://site.mockito.org
[hamcrest]: http://hamcrest.org/JavaHamcrest/
[java_synchronization]: https://docs.oracle.com/javase/tutorial/essential/concurrency/sync.html
