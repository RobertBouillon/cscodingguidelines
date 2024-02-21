# Async / Await

## Guidelines

- Avoid async/await outside of environments with high-volume context switching.

  The main benefit to cooperative multitasking is avoiding context switches. Do not use async/await unless your application's performance profile indicates significant time lost to context switches. There is a cost to async/await that is not recuperated unless the time is gained back by avoiding excessive context switching.
  
  A synchronous API is simpler and functionality is more predictable. Generally speaking, this limits async/await to ASP.NET and network applications.

- Avoid `ValueTask`

  `ValueTask` is a performance-specific optimization. There are [signific considerations and caveats](https://devblogs.microsoft.com/dotnet/understanding-the-whys-whats-and-whens-of-valuetask/) when using `ValueTask`. Avoid the temptation to use it as the default because it does not require an allocation. Only use it where performance has been identified as a concern.

- Avoid adding a `CancellationToken` to a method's parameters unless necessary

  Cancellation should only need to be implemented if it's attached to a specific software feature or requirement. `CancellationToken` adds a lot of boilerplate noise to code that is rarely useful as Task Cancellation is not common. Even with web applications, it's usually fine to just let the request continue altough it's been cancelled - it just means the socket's been closed. Web methods should be short-lived to begin with;  there's likely more cost to adding the checks for the exception to the rule than letting the request run to completion when the exception occurs.

- Prefer to use these NuGet packages

  - https://www.nuget.org/packages/Nito.AsyncEx
  - https://www.nuget.org/packages/System.Linq.Async

- Prefer `Task.GetAwaiter().GetResult()` over `Task.Result`

  The preferred method produces a better stack trace

- Prefer to [elide tasks](https://blog.stephencleary.com/2016/12/eliding-async-await.html) when possible

- Prefer to return `Task.CompletedTask` for methods which run synchronously.

- Disposable objects must implement `IAsyncDisposable` ([link](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-disposeasync)) when applicable


## Costs & Considerations

- Any method that uses the `async` keyword generates a state machine
  
  - State machines promote stack values to the heap, reducing cache efficiency

  - There are two allocations per-call: `Task` and the state machine.

- Async variants of methods create code duplication 
  - Potential for one method to be changed without changing its async counterpart
  - Duplicated development & testing

- Cannot use `out` or `ref` parameters

  - Cannot use `Span<T>` parameters as they're a `ref struct`

- Method chaining is not clean

- LINQ is [slower](https://github.com/JoshClose/CsvHelper/issues/1560), functionality is [limited](https://stackoverflow.com/questions/59689529/return-iasyncenumerable-from-an-async-method), and requires [third-party API's](https://www.nuget.org/packages/System.Linq.Async) to fully utilize.

- [ConfigureAwait](https://devblogs.microsoft.com/dotnet/configureawait-faq/) boiler-plate is required to [prevent deadlocks](https://learn.microsoft.com/en-us/archive/msdn-magazine/2011/february/msdn-magazine-parallel-computing-it-s-all-about-the-synchronizationcontext) 

- Call stack is [not always accessible behind awaited calls](https://learn.microsoft.com/en-us/archive/msdn-magazine/2013/february/async-programming-async-causality-chain-tracking). Notably this limits debugging run-time errors with stack traces.

- Property accessors cannot use async methods or code (accessors are synchronous and using async code can cause deadlocks)

## Common Pitfalls

- Try / Catch / Finally Blocks can create race conditions

  A task that is not immediately awaited can continue to run in spite of an exception being thrown in the calling context, as `catch` and `finally` are executed synchronously and may or may not complete before the task is `awaited`. Thus it is likely that the task runs after `finally` runs, though this is a race condition that is not guaranteed. Care must be taken to ensure that `catch` and `finally` blocks consider that any tasks that are not immediately awaited could potentially execute outside of the block.

- Execution environment can change how code is implemented

  Code that [works properly in a console application may not work correctly in ASP.NET](https://blog.stephencleary.com/2013/11/taskrun-etiquette-examples-dont-use.html) because the [synchronization context](https://learn.microsoft.com/en-us/archive/msdn-magazine/2011/february/msdn-magazine-parallel-computing-it-s-all-about-the-synchronizationcontext#notes-on-synchronizationcontext-implementations) can change the behavior of async code. Care must be taken in the implementation of functionality which may be utilized in different async contexts.

- Synchronous and Asynchronous call contexts are mutually exclusive, and care needs to be taken when switching between them.

  It's intuitive to think you can call `StartTask().Wait()` or `StartTask().Result` and convert an async call to a synchronous call, but this can cause [deadlocks and other unintended behavior](https://learn.microsoft.com/en-us/archive/msdn-magazine/2015/july/async-programming-brownfield-async-development). Async code should be async ["all the way down"](https://learn.microsoft.com/en-us/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way), meaning the root of the call stack should be asynchronous. Consider [the following approaches](https://stackoverflow.com/questions/9343594/how-to-call-asynchronous-method-from-synchronous-method-in-c) when sync and async code must co-exist.

- CPU intensive tasks can also "block"

  Blocking code is not just code that causes the OS thread to enter a sleep state, but any code that can take a long time to execute is considered blocking. Code that blocks can prevent other async tasks from running [depending on the context](https://blog.stephencleary.com/2013/11/taskrun-etiquette-examples-dont-use.html).
  
  Note that some features are inherently long-running tasks, such as compression, encryption, and serialization. This is an inherent drawback to cooperative multitasking - traditional multithreading is better suited to this workload as the thread scheduler can preempt any long-running tasks. 
  
  If a CPU-bound tasks must be run in an async context ([outside of ASP.NET](https://blog.stephencleary.com/2013/11/taskrun-etiquette-examples-dont-use.html)), consider the following approaches.

  - `Task.Yield` can be used when the loop is controlled by the user, however care must be taken as calling this too frequently can starve the current task and impact performance, while calling too infrequently can starve other tasks waiting for time from the task scheduler. 

  - Place the intensive task on a background thread using `Task.Run`

- State machines are required, even if calls are executed synchronousaly.

  An asynchronous method that returns a `Task.CompletedTask` and lacks the `async` keyword is completed synchronously and thus avoids the allocations and overhead incurred by async/await, However by nature of it being an async call, any methods which invoke the method must assume it *could* execute asynchronously, and therefore state machines would be required ["all the way down"](https://learn.microsoft.com/en-us/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way), even if the entire stack ends up being executed synchronously.

- There are no cooperative [synchronization primitives](https://learn.microsoft.com/en-us/dotnet/standard/threading/overview-of-synchronization-primitives).

  General synchronization and the protection of critical sections are crucial for many applications. By nature, threading primitives block, and async methods [should not block](https://blog.stephencleary.com/2012/12/dont-block-in-asynchronous-code.html).


## References

- [Asynchronous programming scenarios (Microsoft)](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios#important-info-and-advice)
- [Task-based asynchronous pattern (TAP) in .NET: Introduction and overview (Microsoft)](https://learn.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)