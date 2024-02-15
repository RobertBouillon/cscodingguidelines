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
* **Avoid** the *Helper* suffix on class names

  Helper classes often miss the context.


## Fields & Properties

* **Avoid** the use of setters on properties that expose collections

  Code that references your property assumes the list will be persistent. Allocating a new list may leave a stale reference to the older list. Public collections should not recreate lists or allow publicly other code to do so.


  **Incorrect**
  ```csharp
  class MyClass
  {
    public List<string> MyCollection { get; set; }

    private void Reset() => MyCollection = new List<string>();
  }
  ```

  **Problem**
  ```csharp
  void Foo()
  {
    NyClass.MyCollection.Add("Foo");
    MyTextBox.Items = MyClass.MyCollection;
    MyClass.Reset();
    MyClass.MyCollection.Add("Bar");
    //MyTextBox.Items = ["Foo"] still because it maintains a reference to the previous List
  }
  ```

  **Correct**
  ```csharp
  class MyClass
  {
    public List<string> MyCollection { get; }      //No setter

    private void Reset() => MyCollection.Clear();  //Instance is cleared instead of being changed.
  }
  ```

* **Avoid** restricting collections to read-only access(`IReadOnlyCollection<>` & `IEnumerable<>`)

  Collections generally do not need to be restricted to read-only. 
  
  - Implementing read-only properties requires the API consumer to create a shallow copy of the collection if they would like to modify it.

  - `List<>` implements `IList<>` and `ICollection<>`, which are not available in the read-only interfaces. Making a collection read-only significantly reduces the usability of the member.

* **Avoid** exposing read-only collections properties as `IReadOnlyCollection<>`. **Prefer** `IEnumerable<>`.

  Using `ReadOnlyCollection<>` communicates to the consumer of the API that the list is persisted, limiting the implementation to methods which persist the data. Changing the implementation of the property to a LINQ expression would require the result of the expression to be presisted.

  `IReadOnlyCollection` is a thin wrapper on top of `IEnumerable<>` that only implements `Count` and counter-intuitively, does not implement `ICollection`, adding little value to its use. LINQ's `Count()` has an optimization that uses the `Count` property of a collection if it exists, so there's no practical reason to expose `ReadOnlyCollection<>` over `IEnumerable<>`. 

  **Example**
  ```csharp
  class MyClass
  {
    private List<int> _data;
    public List<int> Data => _data; //Read-Write List
    public IEnumerable<int> PositiveData => _data.Where(x => x > 0);

    public IReadOnlyCollection<int> Data => _data.AsReadOnly();
    public IReadOnlyCollection<int> PositiveData => _data.Where(x => x > 0).ToList().AsReadOnly();  
  }

  ```


## Constructors
* **Prefer** collection parameters typed as `IEnumerable<>`

  `IEnumerable<>` is the base form to use for a collection of elements and provides the most usability for the constructor.
  
* **Avoid** maintaining a reference to collections passed into the constructor

  A class generally denotes an encapsulated stateful representation. It is uncommon for a class' state to change based on values maintained by another class. In the case where a reference to external data is maintained, the use of a property should be preferred.

  The exception to this rule is a class which is designed to observe data from an external object, and the absence of a reference would cause the state of the observing object to be invalid. In these cases it should be clear to the consumer that a reference to the collection supplied to the constructor is being maintained by the class.

  **Maintain the reference with a property**

  In the following example, the use of a property makes it clear that a reference is being maintained to the original list. Changes to the list would be reflected in `MyListBox`.

  ```csharp
  var stuff = new List<int>(){ 1, 2, 3, 4 };
  MyListBox.DataContext = stuff;
  stuff.Add(5);  //DOES affect MyListBox
  ```

  **Constructor arguments should be transient**

  In the following example, the use of a constructor denotes that the HashSet will be *initialized* with the collection, however changes to the collection will not automatically be reflected in the `HashSet<>`.

  ```csharp
  var stuff = new List<int>(){ 1, 2, 3, 4 };
  var hashSet = new HashSet<int>(stuff);
  stuff.Add(5);  //DOES NOT affect hashSet
  ```

  **An exception to the rule**

  It is clear by the name that `ObervableCollection<>` is designed to observe an existing collection, and the absense of a reference to a collection to observe would result in an invalid state.

  ```csharp
  var stuff = new List<int>(){ 1, 2, 3, 4 };
  var observable = new ObservableCollection<int>(stuff);
  stuff.Add(5);  //DOES affect hashSet
  ```


## Subclasses

* **Prefer** nesting a class inside of another class if it's used privately by the nesting class.

  A class whose purpose is to facilitate the private implementation of another class would be an implementation detail and should be hidden from the API. While `internal` could be used to prevent the class from being seen outside of the library, nesting the class goes one step further and hides the class from other classes in the library; other classes within the library should not be using the nested class, and exposing it internally creates the opportunity for unintended interdependencies.

  Nested classes are preferred for Concrete interface implementations (e.g. `IEnumerable<>`)
  
  Exposing the class internally to the library, even though it's only used for a single class, unnnecessarily clutters the internal API - it's noise.

  **Example**
  ```csharp
  class MyClass : IEnumerable<Foo>
  {
    private class MyClassEnumerator : IEnumerator<Foo>
    {
      public MyClassEnumerator(MyClass source) {}
    }

    IEnumerable<Foo>.IEnumerator<Foo> GetEnumerator() => 
      new MyClassEnumerator(this);
  }

  ```

* **Prefer** nesting event handlers and argument classes

  Event handlers and arguments are usually specific to a single event *member*. It's uncommon for different classes to share the same parameters for an event. Common event handler names are likely to collide within the same namespace (i.e. `ChangedEventArgs`). 
  
  Sharing event handlers can cause breaking changes to the API if event parameters need to be changed for the event of one class, but not another.

  Event handlers should only exist in a namespace when they:
  1. Represent shared functionality (e.g. `CancelEventArgs`)
  2. A change to one event's arguments MUST cascade to all events which share the same argument class.

  **Example Problem**

  In the change proposed below, we must either 
  1. Add Index to the AddedEventArgs, which would impact `ClassA`, even if ClassA has no concept of an Index
  2. Create a new EventHandler class just for `ClassB`. 
  
  Both would be breaking API changes. If these were private classes to begin with, adding `index` would be a non-breaking API change.

  ```csharp
  public class AddedEventArgs : EventArgs
  {
    public Foo Item { get; } 
  }

  public class ClassA
  {
    public event EventHandler<AddedEventArgs> Added;
    public void Add(Item item) => Added?.Invoke(new (item));
  }

  public class ClassB
  {
    public event EventHandler<AddedEventArgs> Added;
    public void Add(Item item) => Added?.Invoke(new (item));
  }

  //Proposed change
  public class ClassB
  {
    //Add int index
    public void Add(Item item, int index) => Added?.Invoke(new (item, index));
  }
  ```


* **Avoid** non-private nested classes

  - When used, non-private nested classes **should not** have public constructors.

  A nested class that is not private is difficult for users to locate as classes are generally assumed to be in namespaces.
  Non-private classes should be reserved for instances where the class is exposed by another member of the class and should not be instanciated publicly.

  **Exceptions**
  
  ```csharp
  class MyClass
  {
    public class AddedEventArgs : EventArgs
    {
      internal AddedEventArgs() {}
    }

    public event global::System.EventHandler<AddedEventArgs> Added;
    protected void OnAdded(object privateName) => OnAdded(new AddedEventArgs(privateName));
    protected virtual void OnAdded(AddedEventArgs e) => Added?.Invoke(this, e);
  }
  ```

  ```csharp
  class MyClass
  {
    //Must be public to be used by public member GetIndexer. Not intended to be used directly by consumers.
    public class Indexer  
    {
      //Should be internal to prevent direct usage by API consumers
      internal Indexer(MyClass source) { }  
    }

    public Indexer GetIndexer() => new Indexer(this);
  }
  ```


