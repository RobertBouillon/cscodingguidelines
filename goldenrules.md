# Golden Rules for Software Engineering

- **You don't solve problems with code**
  
  Code is the means by which solutions are implemented, not designed. The solution should be designed outside of code, first, and then implemented in code. This is an imortant distinction because it's very easy to get stuck in a loop, trying to solve a problem by changing code. This is especially true when implementing complex algorithms. Most complex mathematical algorithms can be expressed and solved far easier in tools other than code. I tend to design and test complex algorithms in Excel, first. MatLab is also a good option.
  
  If your algorithm is not working properly in code, the first instinct of most programmers (myself included), is to debug the code; to find and tune the lines of code that are misbehaving. It's much easier to compare your code to the design to find errors than to try to debug the code to find errors. If it works on paper (or Excel, or whatever), it should work in code. Code is for implementing solutions - not designing them.
  
  The same rules hold true when solving larger business problems. In most cases, the solution to a business problem is, in-fact, a process change, not a software change. Software ultimately **automates** the business process, but foremost it's the buisiness process that has changed, not the software. When designing business software, the same rule applies - solve the problem outside of code, first. Identify which business processes need to be improved, how the problem is being solved today, and how the proposed solution would affect (and improve) the business process. 

- **Code is a liability**

  Every line of code has the potential to be a bug. Every line of code needs to be visually processed by a developer, maintained and debugged, and worked by a compiler. This is why product managers can often shoot-down ideas for new features. Consider the entire cost of the feature, including testing & maintenance, before deciding to put it in.
  
  This **does not** mean that fewer lines of code is better, only that superfluous features should be ommitted from functionality.

- **If it doesn't add value, remove it**

  Our brains are pattern-recognition machines, and the more code (and comments) that exist, the less efficient the machine operates.
  
  This seems intuitive, but we do unnecessary work all the time for all the wrong reasons. Take code comments, for example: some standards require comments for **everything**, but are they **all** necessary? Probably not. In-fact, with C#, I find that well-written code requires very few comments, and if the code doesn't "tell a story", then it's probably worth revising. Before adding a comment, ask yourself if the code would benefit from refactoring, instead. Is it better to create a comment for a block of code, or refactor it to a method?
  
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
  
  *If the justification is that there's a rule requiring that all code be commented, then it's a stupid rule and the rule needs to change. No rules should require you to perform work that does not add value. If boiler-plate comments are really necessary, the effort is better invested in code that can generate documentation where codecomments are absent.*
   
- **Iterative development creates technical debt**  

  Every new feature you add creates technical debt. Every. Single. One. The nature of patterns is that they represent information that is repeated. In iterative development, as we sporatically add to a code base over time, new patterns will emerge. 
  
  You must periodically review the code for technical debt to simplify the code base, extrapolating patterns and refactoring to simplify the code.

  *"Perfection is acheived not when there's nothing left to add, but when there's nothing left to take away. --Antoine de Saint-Exupery"* 

  A product is done when it is "Feature Complete." Software is done when there is nothing left to refactor.
    
- **A *Minimum Viable Product* should apply to the features of an application, not the implementation**
  
  MVP is not a good way to avoid over-engineering, as it's often an excuse to forego testing, documentation, and refactoring of complex code.
  
  The essence of over-engineering is solving problems that aren't problems yet, however 80% of our job is knowing what *could* break as a result of the code we write, and covering all of those cases. Predicting, and preventing, what could break is built into how we think.  It takes years of experience to know where to draw the line with new features, and how to implement only what's necessary, nothing more, and nothing less.
  
  There are no "hard and fast" rules to prevent over-engineering, however MVP is often used as the basis for determing what work goes into the implementation of a feature. MVP should be used to determine what features to include as part of a product. It should not be used as an excluse to cannibalize the effort required to harden code and resolve technical debt.
  
  If you want high-quality code, don't use MVP to prevent engineers from working on code. Hire good, experienced senior engineers, and let them guide the discussions. 

- **Good code should read like a good book, and should tell a good story**

  Reading code shouldn't be like deciphering hyroglyphics. This is especially applicable to C#, which has best-in-class support for object-oriented principles. 
  
  Code should be simple.
  
- **Good code should be either intuitive or well-documented**

  If adding a web page to your application requires 4 steps to work properly, those 4 steps should be documented. Don't leave it to the next developer to "figure out" how you did something, because the "next developer" might be you in 18 months, trying to figure out what you did and why. Don't rely on error handling to lead the next engineer to know what's going on - document it. If you're concerned that documentation is a waste of time, remember that it will take less time to document something than for the next developer to debug it to figure out what you should have already documented.

