# Golden Rules for Software Engineering

- **Code is a liability**

  Every line of code has the potential to be a bug. Every line of code needs to be visually processed by a developer, maintained and debugged, and worked by a compiler. This is why product managers can often shoot-down ideas for new features. Consider the entire cost of the feature, including testing & maintenance, before deciding to put it in.
  
  This **does not** mean that fewer lines of code is better, only that superfluous features should be ommitted from functionality.

- **If it doesn't add value, remove it**

  This seems intuitive, but we do it all the time. Our brains are pattern-recognition machines, and the more code (and comments) that exist, the less efficient the machine operates.
  
  The most common example is with code comments that look like this:
  ```
  ///<summary>
  ///  Foo the bar
  ///</summary>
  ///<param name="bar">The bar to be Foo'd</param>
  /// <returns>The Food bar</returns>
  public Snickers Foo(Bar bar)
  {
  
  }
  ```
  
  The code does not add any more information about the function that doesn't already exist. If you find that you have a function that requires comments, it's likely that the method should be refactored.
  
  *If the justification is that there's a rule requiring that all code be commented, then it's a stupid rule and the rule needs to change. No rules should require you to perform work that does not add value. If boiler-plate comments are really necessary, the effort is better invested in code that can generate documentation where codecomments are absent.*
   
- **Iterative development creates technical debt**  

  Every new feature you add creates technical debt. Every. Single. One. The nature of patterns is that they represent information that is repeated. In iterative development, as we sporatically add to a code base over time, new patterns will emerge. 
  
  You must periodically review the code for technical debt to simplify the code base, extrapolating patterns and refactoring to simplify the code.

  *"Perfection is acheived not when there's nothing left to add, but when there's nothing left to take away. --Antoine de Saint-Exupery"* 

  A product is done when it is "Feature Complete." Software is done when there is nothing left to refactor.
    
- **A *Minimum Viable Product* should apply to the features of an application, not the implementation*
  
  MVP is not a good way to avoid engineering, as it's often an excuse to forego testing, documentation, and refactoring of complex code.
  
  The essence of over-engineering is solving problems that aren't problems yet, however 80% of our job is knowing what *could* break as a result of the code we write, and covering all of those cases. It takes years of experience to know where to draw the line with new features. 
  
  There are no "hard and fast" rules to prevent over-engineering, however MVP is often used as the basis for determing what work goes into the implementation of a feature. MVP should be used to determine what features to include as part of a product. It should not be used as an excluse to cannibalize the effort required to harden code and resolve technical debt.

- Good code should read like a good book, and should tell a good story

  Reading code shouldn't be like deciphering hyroglyphics. This is especially applicable to C#, which has best-in-class support for object-oriented principles. 
  
  Code should be simple.
  
  
