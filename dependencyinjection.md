# Dependency Injection

The use-cases for DI are few, and while DI *can* make code easier to write, it makes code more difficult to read, maintain, and debug. Before you take on the costs of DI, be sure you completely understand the costs, and ensure that DI is the appropriate solution to your problem.

## Costs of Dependency Injection

* No design-time tracability

  While writing code, it's very useful to "Find All References" on a method to see where the method is called. Because methods that use DI are invoked by a bootstrapper that handles injection, there are no symbol references in code, and "Find All References" will not yield any results. You will need to run the application to view the code paths that invoke a particular method. If your code is already using reflection to invoke methods, then this cost is moot.
  
* Single-Instance per-type

  Objects marked for injection are identified by type. If your methods need different instances of the same type, you're forced to either [1] Update the bootstrapper to recognize additional reflected criteria, such as Attributes, or [2] Create different types that represent different instances of the same base type. Both make code more difficult to read and maintain, and eat away quickly at the benefits of using DI.

## Alternatives to Dependency Injection

* Static Members

  If an object is truly a single-instance variable, use the [Singleton pattern](https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Design-Patterns-Singleton).

* Thead-Static Members

  A [thread-static](https://docs.microsoft.com/en-us/dotnet/api/system.threadstaticattribute?view=netframework-4.8) member is a static class that is bound to the context of the executing thread, rather than the process. This is a superior alternative to DI in most circumstances.

## Thread-Static Example

```csharp
public static class WebRequest
{
  [ThreadStatic]
  public static Url Url { get; set; }
}

public class WebServer
{
  private Dictionary<string, Func<string>> WebPages { get; }
  public void OnRequest(string url)
  {
    //Set the context for the callee
    WebRequest.Url = url;
    var page = ParsePageFromUrl(url);
    WebPages[page]();
  }
}

public class WebPage
{
  public string Render() => $"You're visiting {WebRequest.Url}";
}

```
