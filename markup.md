# Code Markup Guidelines

## In-Line Documentation Comments
C# offers in-line code comments via [codedoc](https://docs.microsoft.com/en-us/dotnet/csharp/codedoc). **Do _not_ use them.** 

Codedoc comments are useful for two scenarios: documentation and intellisense. For the many times you'll be working in the code, the comments are only going to get in the way. Code comments are noise that will make it more difficult for your brain to "parse" the code, and consume valuable real-estate from the screen that could be used to show code - you know, the stuff you actually care about while working in the file. Interfaces (i.e. method signatures) rarely change, thus code comments should rarely change.

Codedoc was created in a time when well-documented code was a hallmark of a seasoned engineer, and were thus revered. Times have changed, and we recognize that code is better when you don't need documentation to understand what it's doing (that's arguably C#'s greatest strength). 

Codedoc was also conceived in a time where the IDE's primary role was to support code compilation. Nowadays, the IDE can assist in nearly every aspect of a developer's work, thanks to robust extensibility.

Class documentation should be relegated to an IDE window, and probably stored in a structured data file or md, but not in the code. If you **must** add code comments, at least use the IDE to collapse them.

## Regions

- PREFER use regions to group similar members together (fields, properties, constructors, methods, etc)
- AVOID using regions to group similar members for small files
- PREFER to use regions to group members for implemented interfaces
- CONSIDER using regions to group members that perform functions for a common feature, such as serialization
- DO NOT mix region usage: either use regions to group all member types or none of them
- DO use regions to wrap subclasses within a class, regardless of member type regions
- PREFER regions to contain parameter validation.
- DO NOT using regions inside members, (except for parameter validation) - if you need a region in a method, you should refactor that region to a private method, instead.

