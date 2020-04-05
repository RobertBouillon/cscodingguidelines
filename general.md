# General Guidelines

## Naming

* Names should not reflect metadata otherwise declared by statements.

  The name of a symbol should not be dependent upon its metadata.

  * Camel Casing
  
    C# is a strong-typed language, thus it is the IDE's responsibility to provide the developer with type information (CTRL+K, CTRL_I in Visual Studio). Types can (and often do) change during development, which would require a name change as well. Given the variety of types in .NET, abbreviations for types are more likely to be confusing than informative, and the variable name *should* provide enough context to a developer that they can infer the type without a prefix.

  * Class Prefixes and Suffixes for abstract or inheriting
  
