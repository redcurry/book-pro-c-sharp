Notes on C# from Pro C# Book
============================

Functions
---------

Named parameters
................

    public int Add(int first, int second) { ... }
    Add(second: 10, first: 20);

Classes
-------

Chaining constructors
.....................

    public MyClass() : this(10) {}
    public MyClass(int i) : this(10, 20) {}
    public MyClass(int i, int j) { ... }

Static constructor
..................

Used to initialize `static` fields.
There can only be one and cannot take any parameters.
Called before any object of that class is instantiated
or before any static member is called.

### Static class

Can only contain static members.
Cannot be instantiated.
Used for utility classes.

### Access modifiers

public types (e.g., classes) can be accessed from external assemblies.
internal types cannot be accessed from external assemblies
  (only nested types can be private).

### Automatic properties

    class Car
    {
        // Automatic properties must be both read and write
        public string PetName { get; set; }
        public int Speed { get; set; }
    }

### Initialization syntax

Initialize an object using its properties:

    Point p = new Point { X = 30, Y = 20 };
    Point p = new Point() { X = 30, Y = 20 };
    Point p = new Point(10, 15) { X = 30, Y = 20};

In the first and second initializations, the default constructor is called,
then each property is set.
In the third initialization, a custom constructor is called,
then each property is set (overriding the previous values).
A more complex example:

    Rectangle r = new Rectangle
    {
        TopLeft = new Point { X = 10, Y = 10 },
        BottomRight = new Point { X = 200, Y = 200 }
    };

### Const and read-only fields

`const` members are implicitly `static` and must be defined at compile-time.
`readonly` fields are like `const`, but may be defined at runtime
  in the constructor only; also, they are not implicitly `static`.
A `readonly` field can be `static` and initialized in a static constructor.

## Inheritance

Structures cannot be derived; they are sealed.

### Multiple inheritance

Not allowed, except interfaces.

### Keyword base

Use for calling base constructor:

    public Manager(...) : base (...) { ... }

### Nested (or inner) types

Can access private members of the outer class.

## Polymorphism

`virtual`: method is allowed to be overridden (but does not have to be).
`override`: method overrides a virtual method.
Can call base's method, like `base.method()`.
`sealed`: when used on a virtual method, prevent it from being overridden
`abstract`: when used on a class, prevent instantiation;
    when used on a method, does not provide implementation
    and must be implemented by a concrete subclass
    (abstract methods can only be present in abstract classes)
`new`: when used on a method, specifies that method does not override
    the parent's method, but it is a completely new method that
    shadows (hides) the parent method
`as` keyword: Cast to specified type, if it can't, set it to `null`
`is` keyword: Test whether an object is of the specified type

## System.Object

`Equals()` compares object references by default.
If overriding `Equals()`, should also override `GetHashCode()`.
For the hash code, could use `GetHashCode()` on an important field
    in the class, or on the `ToString()` method of the class.
`static Equals()` in object calls `Equals()` in one of the passed objects,
    and `static ReferenceEquals()` in object
    take two objects and compares what they're pointing to

(Skipped Chapter 7. Exceptions)

Interfaces
----------

* Interface names should start with an "I".

* Interfaces contain *only* abstract member definitions
  (methods, operators, properties, events, and an indexer).

* Structures can implement interfaces.

* Can use "is" to test whether an object implements an interface.

* Explicit interface implementation of methods requires that such methods
  be called on a reference of the interface type, not on the object type.

  * General pattern: returnType InterfaceName.MethodName(params) { }
  * Note that there is no access modifier---method is implicitly private

IEnumerable
...........

* Implement IEnumerable if you want a class to be used in a foreach statement.

  * Simplify the implementation by returning GetEnumerator() from the
    underlying collection object (such as an array).

* IEnumerable exposes GetEnumerator(), which returns an IEnumerator.

* IEnumerator exposes MoveNext(), Current, and Reset().

* GetEnumerator() may also be implemented with yield statements,
  each one returning the next item to be accessed in a foreach statement.

* Also, any method can use yield statements; such a method must return
  an IEnumerable interface and can therefore be used in a foreach statement.

ICloneable
..........

* Implement the Clone() method in the ICloneable interface to be able to
  copy an object in a specific way (such as a deep copy).

  * Member-by-member copy (typical when class contains only value types)
    can be simplified by using the method MemberwiseClone() in object.

IComparable and IComparer
.........................

* Implement CompareTo() in the IComparable interface to allow
  a class to be sorted (for example, using Array.Sort()).

  * If it makes sense, can use the CompareTo() method of an internal object
    to simplify the implementation.

* To implement additional ways to compare two objects, create a new "helper"
  class that implements IComparer (exposes the method Compare()).

  * Can specify comparer object in Array.Sort().
  * Can provide an IComparer as a static property for ease-of-use.


Collections and Generics
------------------------

* Prefer the generic versions of the collection classes.

* Problems with non-generic collections:

  * Boxing/unboxing of value types is costly
  * No type safety (must cast  from object type)
  * Tedious to create specialized collections

* Some nongeneric classes have generic methods, such as ``Array``
  (e.g., ``Sort<T>()``).

* There are generic versions if IComparable, IEnumerable, etc.

* Collections and generic collections can use *collection initialization*
  syntax if the collections support an ``Add()`` method
  (in ``IList`` and ``ICollection<T>``).

* Use ``ObservableCollection<T>`` to handle the event when the collection
  changes (add, remove, reset, etc.).

* ``default(T)`` returns the default values for type T

   * For numeric values, this is 0.
   * For reference types, this is null.
   * For structures, it sets all fields to their default values.

* ``where`` keyword constraints the type T.

  * ``public class MyGenericClass<T> where T : class, IDrawable, new()``
    means that type ``T`` must be a reference type,
    must implement ``IDrawable``, and must have a default constructor
    (the ``new()`` constraint must always be listed last).
  * ``static void Swap<T>(ref T a, ref T b) where T : struct``
    means that ``T`` must be a structure (or ``System.ValueType``).

* Cannot perform operations (+, -, \*, /, etc.) on ``T``
  (unless ``T`` implements an interface that defines such operations).

Delegates
---------

* A delegate is a class that points to a method (or list of methods),
  which can be ``static`` or belong to a specific object.

* A delegate maintains the following: the address of the method it points to,
  the parameters of the method, and the return type of the method.

* Delegates can call the methods it points to asynchronously
  (on a separate thread), without having to manually create thread objects.

* The following delegate can point to any method that takes two integers
  and returns an integer: ``public delegate int BinaryOp(int x, int y);``

* The C# compiler automatically creates a sealed class deriving from
  ``System.MulticastDelegate`` (which itself derives from ``System.Delegate``),
  which contains the infrastructure for holding on to a list of methods.

* Create a delegate object by sending it the method in its constructor:
  ``BinaryOp b = new BinaryOp(SimpleMath.Add);``

* Invoke the method by calling the delegate: ``b(10, 10)``
  or ``b.Invoke(10, 10)``.

* A list of methods a delegate points to can be obtained
  with the ``GetInvocationList()`` method of the delegate.

* Set the method pointer to a delegate by assigning it the method itself.

* Add a method pointer to a delegate using the += operator
  and specifying the name of the method directly
  (i.e., there is no need to create a new delegate object).

* Delegates can be generic, e.g.,
  ``public delegate void MyGenericDelegate<T>(T arg);``

* Can use canned delegates rather than create custom ones:
  ``Action<>`` for void-returning methods and ``Func<>``
  for methods that return something (specify return type as the last
  type parameter).

Events
------

* Events simplify callback functionality by automatically
  allowing callers to register or unregister methods
  using operators += and -=.

* Define the delegate signature, then provide an event::

      public delegate void CarEngineHandler(string msg);
      public event CarEngineHandler Exploded;
      public event CarEngineHandler AboutToBlow;

* Register a method to an event::

      car.AboutToBlow += CarAboutToBlow;    // CarAboutToBlow is a method

* Many events use delegates whose first parameter is the object
  initiating the event and the second parameter a subclass of ``EventArgs``::

      public delegate void CarEngineHandler(object sender, CarEventArgs e);

* There is an ``EventHandler<T>`` delegate whose first parameter
  is already an object and second parameter is the custom ``EventArgs`` type,
  so there is no need to define a delegate::

      public event EventHandler<CarEventArgs> Exploded;

* Can directly register a block of code to an event ("annonymous method")::

      // Parameters are optional
      car.AboutToBlow += delegate(object sender, CarEventArgs e)
      {
          // Do something
          // Can access variables of the outer method and class
      };

Lambda expressions
------------------

* Lambda expression is a simplified syntax for annonymous methods.

* Instead of saying::

      List<int> evenNumbers = list.FindAll(delegate(int i)
      {
          return (i % 2) == 0;
      }

  One can say::

      List<int> evenNumbers = list.FindAll(i => (i % 2) == 0);

* The compiler can figure out the type of the parameter based on the delegate,
  but it can also be specified, like ``(int i) => (i % 2) == 0``.

* Can contain multiple lines::

      List<int> evenNumbers = list.FindAll(i =>
      {
          bool isEven = ((i % 2) == 0);
          return isEven;
      }

* Can have zero or multiple parameters::

      m.SetMathHandler((msg, result) => { // Do something });
      m.SetSimpleHandler(() => { // Do something });

* Can use lambda expression to register to an event::

      car.AboutToBlow += (sender, e) => Console.WriteLine(e.msg);

WPF
---

* Attribute ``[STAThread]`` in ``Main()`` ensures legacy COM objects
  are thread safe; it is necessary.

* Creating a new Window object automatically adds it to Application.Windows.

* The Content property of a Window should be a layout manager
