# Exception Handling Guidelines for C#

## General Guidelines
Exceptions are application errors and should not be used to communicate information during normal, expected operations. An exception means that **an unrecoverable condition has occurred**. As such, do not *throw* an exception unless an unrecoverable condition has occurred.

When an exception is thrown, it has to perform a number of [expensive operations](https://mattwarren.org/2019/01/21/Stackwalking-in-the-.NET-Runtime/) designed to assist a developer in debugging and diagnosing a problem. If an exception is used to communicate information during a normal operation, then all of this work is an unnecessary and expensive performance hit that will silently impact your application's performance.

When debugging, you should be able to enable **Break on Exception** in your IDE and effectively debug any issues in real-time. If you're throwing exceptions that aren't *really* errors, you spend a lot of time continuing past exceptions that aren't *really* exceptions until you get to the exception you're trying to debug. Java applications throw exceptions liberally, which is why most Java applications rely exclusively (or at least primarily) on trace logging: in-process debugging is difficult. While it's possible to configure the IDE to only break on the error that meets a tight criteria for the issue being debugged, it's onerous and imperfect. 

If you want to be able to debug your application, don't throw unnecessary exceptions.

## Try / Catch
- **AVOID** Exception handling in member functions. 

  As a general rule, exceptions should be caught where they are handled, and this is usually at the top of the call stack where the error is either logged or displayed to the user.

- **AVOID** Catching and re-throwing exceptions (Catch and release)

  You should only catch and re-throw if the original exception does not accurately describe the problem, or the exception being thrown lacks the information required to diagnose the underlying problem

- **AVOID** Custom exception classes

  Since exceptions should not be used to communicate normal, expected operations, receiving an exception should only result in a single exception flow. Changing the exception flow based on the type of exception is a deprecated pattern and generally indicates a poorly designed API that communicates via exception instead of returning data. As such, if you're creating a custom exception so that it can be handled differently than other exceptions, it probably indicates that the API should be returning data rather than raising an exception. As a rule, if it's not adding value, don't do it.

- **PREFER** Existing exception classes over custom classes for returning additional data.

  The Message property should describe the nature of the problem. If the exception *can* be described in a string, it *should* be. 
  
  If the objection is that the string cannot be parsed, then that means that you expect the receiver to act upon the result of the exception, in which case it should be considered part of the *expected flow* of an API, is inherently recoverable, and shouldn't raise an exception.
  
 
 
