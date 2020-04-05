# Class Design Guidelines

## Naming

* **Avoid** the *Base* suffix on class names

  A class that inherits a base class is a specialization of the base class. The specialized class should specify *how* it is specialized in its name, making the *Base* suffix in the base class superfluous.
  
  ```csharp
  //Correct Examples
  class ProgrammingLanguage {}
  class CSharp : ProgrammingLanguage {}
  
  class Object {}
  class ValueType : Object {}
  class Integer : ValueType {}
  
  class WebRequest {}
  class HttpWebRequest : WebRequest {}
  
  //Incorrect Examples
  class ProgrammingLanguageBase {}
  class CSharp : ProgrammingLanguageBase {}
  
  class WebRequestBase {}
  class HttpWebRequest : WebRequestBase {}
  ```
