# DedicatedThreadPool
An instanced, dedicated thread pool for eliminating "noisy neighbor" problems on the CLR `ThreadPool`.

## Installation

You can install `DedicatedThreadPool` via NuGet!

```
PS> Install-package Helios.DedicatedThreadPool
```

This package doesn't install any binaries, just the following C# file: `Helios.Concurrency.DedicatedThreadPool.cs` - which contains the `DedicatedThreadPool`, `DedicatedThreadPoolTaskScheduler`, and `DedicatedThreadPoolSettings` classes.

## API

You can create a `Helios.Concurrency.DedicatedThreadPool` instance via the following API:

```csharp
using (var threadPool = new Helios.Concurrency.DedicatedThreadPool(
        new DedicatedThreadPoolSettings(numThreads)))
{
    threadPool.QueueUserWorkItem(() => { ... }));
}
```

This creates a `DedicatedThreadPool` object which allocates a fixed number of threads, each with their own independent task queue.

This `DedicatedThreadPool` can also be used in combination with a `DedicatedThreadPoolTaskScheduler` for TPL support, like this:

```csharp
//use 3 threads
var Pool = new DedicatedThreadPool(new DedicatedThreadPoolSettings(3));
var Scheduler = new DedicatedThreadPoolTaskScheduler(Pool);
var Factory = new TaskFactory(Scheduler);

 var task = Factory.StartNew(() =>
{
    //work that'll run on the dedicated thread pool...
});

task.Wait();
```

> NOTE: `DedicatedThreadPool` is marked as `internal` by default, so it can be used opaquely across many dependent projects.

## Benchmark

Latest benchmark on our build server (2 core Windows Azure A2 medium)

### Helios.DedicatedThreadPool v0.3

          Metric |       Units / s |         Max / s |     Average / s |         Min / s |      StdDev / s |
---------------- |---------------- |---------------- |---------------- |---------------- |---------------- |
[Counter] BenchmarkCalls |      operations |    9,998,100.36 |    8,661,529.31 |    7,093,003.46 |      944,954.17 |

### System.Threading.ThreadPool
          Metric |       Units / s |         Max / s |     Average / s |         Min / s |      StdDev / s |
---------------- |---------------- |---------------- |---------------- |---------------- |---------------- |
[Counter] BenchmarkCalls |      operations |    2,651,289.19 |    2,510,678.63 |    2,098,882.55 |      179,585.24 |

## License

See [LICENSE](LICENSE) for details.

Copyright (C) 2015-2016 Roger Alsing, Aaron Stannard, Jeff Cyr