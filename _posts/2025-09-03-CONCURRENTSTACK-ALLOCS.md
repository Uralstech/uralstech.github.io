---
layout: post
title:  "ConcurrentStack allocates"
tags: [C#,Note]
---

this is probably insanely obvious to everyone but like this was so annoying
<!--more-->

So I was messing around with code for a project which I do plan to release soon (related to ezrSquared, maybe? :3)
and I wanted to make a custom pooling system for `StringBuilder`. It's probably overkill but hey I wanted to learn
new stuff with it.

So, I first made a really simple static class with a generic type argument which would use a concurrent stack for
the actual pool. Something like this:

```csharp
public static class Pool<T> where T : new()
{
    private static readonly ConcurrentStack<T> s_pool = new();

    public readonly struct Item : IDisposable
    {
        public T Value { get; }
        public void Dispose() => s_pool.Push(Value);
    }

    public static RentedItem Rent() =>
        new RentedItem(s_pool.TryPop(out T? result) ? result : new T());
}
```

I wanted to know more about benchmarking so I figured it'd be a good idea to compare the difference between having
the `Item` subclass of the pool as a readonly struct and as a class. I got a benchmark like this:

```csharp
[MemoryDiagnoser]
public class Benchmark
{
    public static class ClassPool<T> where T : new()
    {
        private static readonly ConcurrentStack<T> s_pool = new();

        public class Item(T value) : IDisposable
        {
            public T Value { get; } = value;
            public void Dispose()
            {
                s_pool.Push(Value);
                GC.SuppressFinalize(this);
            }
        }

        public static Item Rent() =>
            new(s_pool.TryPop(out T? result) ? result : new T());
    }

    public static class StructPool<T> where T : new()
    {
        private static readonly ConcurrentStack<T> s_pool = new();

        public readonly struct Item(T value) : IDisposable
        {
            public T Value { get; } = value;
            public readonly void Dispose() => s_pool.Push(Value);
        }

        public static Item Rent() =>
            new(s_pool.TryPop(out T? result) ? result : new T());
    }

    [Benchmark]
    public void ClassPoolBenchmark()
    {
        using var item = ClassPool<Random>.Rent();
        var rand = item.Value;
        _ = rand.Next();
    }

    [Benchmark]
    public void StructPoolBenchmark()
    {
        using var item = StructPool<Random>.Rent();
        var rand = item.Value;
        _ = rand.Next();
    }
}
```

And, obviously, the class-based pool was allocating a ton more than the struct-based one.
BUT - the struct-based pool WAS STILL ALLOCATING.

So, since I've started verifying all my FOSS code with Gemini 2.5 Pro, I obviously asked why this was the case.

Now, since the convo is like 20 messages or something (I spent way too long on this), I just asked Gemini to sum
up the debugging steps we took lmao

Gemini's first guess was pretty logical: the pool must be starting empty. On the very first run, it's forced to call new Random(), which allocates. That makes sense, right? The 32 bytes was for the Random object, and the extra 24 bytes in the class version was for the wrapper Item class. Okay, cool.

So to test this, we made a new benchmark. The idea was to pre-populate the pool with a bunch of items so it would NEVER be empty during the actual test. We'd use [IterationSetup] to make sure the pool was reset before every single measurement. The code looked something like this:

```csharp
[IterationSetup]
public void Setup()
{
    // Clear the pool and pre-fill it before every run
    StructPool<StringBuilder>.s_pool.Clear();
    for (int i = 0; i < 16; i++)
    {
        StructPool<StringBuilder>.s_pool.Push(new StringBuilder());
    }
}

[Benchmark]
public void StructPool_Correct()
{
    using var item = StructPool<StringBuilder>.Rent();
    item.Value.Append('A', 100);
}
```

it still allocated 32 bytes

At this point we were both losing our minds (awfully ironic coming from someone that doesn't have a mind to lose). How is that even possible? The pool is full, the struct is on the stack, what is there to allocate?? We went through a whole list of theories:

- Theory 1: The Build is Cached. Maybe, just maybe, the benchmark runner was executing an old, cached version of the code that still used Random. It's a classic problem. So I cleaned the solution, nuked the bin and obj folders, and ran it again. Still 32 bytes. (nah personally i'd trust the .NET and BenchmarkDotNet devs with my sanity)
- Theory 2: The Delegate. I had added a Deinitializer to my pool to reset the StringBuilders. It looked like sb => sb.Clear(). Gemini's next guess was that creating this lambda was allocating a new delegate object on every setup. This seemed SUPER promising. But then I hit it with the uno reverse card: "bruh why did it happen with the Random class too". The Random class version had no deinitializer. The theory fell apart.
- Theory 3: The Benchmark Itself is Allocating. We thought maybe BenchmarkDotNet was doing some weird overhead calculation that ran our code before the setup, found the pool empty, and allocated. This was getting into conspiracy theory territory but we were desperate.

I was getting so fed up that I was ready to just write my own ConcurrentStack (no lol). But then I had an idea. On a total whim, I just tried swapping out the ConcurrentStack<T> for a ConcurrentQueue<T>. (fr)

yeah so anyways this is the result of the final benchmark which looks like this:
```csharp

BenchmarkRunner.Run<Benchmark>();

static class StackPool<T> where T : new()
{
    private static readonly ConcurrentStack<T> s_pool = new();

#if NET9_0_OR_GREATER
    private static readonly System.Threading.Lock s_deinitLock = new();
#else
    private static readonly object s_deinitLock = new();
#endif

    private static Action<T>? s_deinitializer;
    public static Action<T>? Deinitializer
    {
        get => s_deinitializer;
        set
        {
            lock (s_deinitLock)
                s_deinitializer = value;
        }
    }

    public readonly struct Item(T value) : IDisposable
    {
        public T Value { get; } = value;
        public readonly void Dispose()
        {
            if (s_deinitializer is not null)
                s_deinitializer(Value);

            s_pool.Push(Value);
        }
    }

    public static Item Rent() =>
        new(s_pool.TryPop(out T? result) ? result : new T());
}

static class QueuePool<T> where T : new()
{
    private static readonly ConcurrentQueue<T> s_pool = new();

#if NET9_0_OR_GREATER
    private static readonly System.Threading.Lock s_deinitLock = new();
#else
    private static readonly object s_deinitLock = new();
#endif

    private static Action<T>? s_deinitializer;
    public static Action<T>? Deinitializer
    {
        get => s_deinitializer;
        set
        {
            lock (s_deinitLock)
                s_deinitializer = value;
        }
    }

    public readonly struct Item(T value) : IDisposable
    {
        public T Value { get; } = value;
        public readonly void Dispose()
        {
            if (s_deinitializer is not null)
                s_deinitializer(Value);

            s_pool.Enqueue(Value);
        }
    }

    public static Item Rent() =>
        new(s_pool.TryDequeue(out T? result) ? result : new T());
}

[MemoryDiagnoser]
public class Benchmark
{
    [GlobalSetup]
    public static void Setup()
    {
        StackPool<StringBuilder>.Deinitializer = sb => sb.Clear();
        QueuePool<StringBuilder>.Deinitializer = sb => sb.Clear();

        using var sb = StackPool<StringBuilder>.Rent();
        sb.Value.EnsureCapacity(100);

        using var qb = QueuePool<StringBuilder>.Rent();
        qb.Value.EnsureCapacity(100);
    }

    [Benchmark]
    public void StackPool()
    {
        using var sb = StackPool<StringBuilder>.Rent();
        sb.Value.Append('A', 100);
    }

    [Benchmark]
    public void QueuePool()
    {
        using var sb = QueuePool<StringBuilder>.Rent();
        sb.Value.Append('A', 100);
    }
}
```

Oh yeah we also switched from using Random to StringBuilder somewhere along the way since that's the reason I made the pool in the first place

![benchmark results](/assets/img/2025-09-03-CONCURRENTSTACK-ALLOCS/benchmark.png)

oh and this is why the stack allocs (and is a tiny bit slower), according to Gemini
- ConcurrentQueue<T> is like a big circular conveyor belt (an array). When it's cleared and reused, threads just start putting items back onto the same belt. It doesn't need to create anything new.
- ConcurrentStack<T> is more like a cafeteria plate dispenser. It's a chain of little boxes ("segments"). When you Clear() the stack, you're essentially throwing away the entire dispenser mechanism. The very first time you push a plate back on, it has to allocate a new box with a new spring to hold that plate.
