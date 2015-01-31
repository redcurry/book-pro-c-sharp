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

Static class
............

Can only contain static members.
Cannot be instantiated.
Used for utility classes.

Access modifiers
................

public types (e.g., classes) can be accessed from external assemblies.
internal types cannot be accessed from external assemblies
  (only nested types can be private).

Automatic properties
....................

    class Car
    {
        // Automatic properties must be both read and write
        public string PetName { get; set; }
        public int Speed { get; set; }
    }

Initialization syntax
.....................

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

Const and read-only fields
..........................

`const` members are implicitly `static` and must be defined at compile-time.
`readonly` fields are like `const`, but may be defined at runtime
  in the constructor only; also, they are not implicitly `static`.
A `readonly` field can be `static` and initialized in a static constructor.

Inheritance
-----------

Structures cannot be derived; they are sealed.

Multiple inheritance
....................

Not allowed, except interfaces.

Keyword base
............

Use for calling base constructor:

    public Manager(...) : base (...) { ... }

Nested (or inner) types
.......................

Can access private members of the outer class.

Polymorphism
............

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

System.Object
.............

`Equals()` compares object references by default.
If overriding `Equals()`, should also override `GetHashCode()`.
For the hash code, could use `GetHashCode()` on an important field
    in the class, or on the `ToString()` method of the class.
`static Equals()` in object calls `Equals()` in one of the passed objects,
    and `static ReferenceEquals()` in object
    take two objects and compares what they're pointing to

Exceptions
----------

* The ``TargetSite`` property of an ``Exception`` returns a ``MethodBase``
  object, representing the method or property that threw the exception.

* The ``Data`` property of an exception, returns an object that
  inherits ``IDictionary``, so one can store additional data there.

* When creating custom exceptions, derive from ``System.ApplicationException``
  so that you know the exception came from the application and not from
  .NET (which inherit ``System.SystemException``).

* Use the ``exception`` snippet in Visual Studio to create
  a .NET best practices exception.

* ``catch (SpecificException e)`` will catch only exceptions deriving
  from ``SpecificException``, ``catch (Exception e)`` will catch any exception,
  and ``catch`` by itself will catch any exception, but not give you the
  exception object.
  
* Rethrow an exception by simply saying ``throw``
  (no need to specify the object).

* An exception may be thrown by code within a ``catch`` block;
  throw a new exception by specifying an inner exception::

      catch (CarIsDeadException e)
      {
          try
          {
              FileStream fs = File.Open(@"C:\errors.txt", FileMode.Open);
              ...
          }
          catch (Exception e2)
          {
              // Throw an exception that records th new exception,
              // as well as the message of the first exception
              throw new CarIsDeadException(e.Message, e2);
          }
      }

* A ``finally`` block will always run, whether an exception occurred or not::

    try
    {
        ...
    }
    catch(Exception e)
    {
        ..
    }
    finally
    {
        // This will always run, exception or not
        ...
    }

  Put logic in a ``finally`` block to do clean up (e.g., dispose of objects,
  close a file, detach from a database).

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

Indexer
-------

* Define similarly to a property::

      public Person this[int index]
      {
          get { return (Person)_people[index]; }
          set { _people.Insert(index, value); }
      }

* The index could be of any type.

* Indexers may be overloaded (as in ``DataTableCollection`` in ADO.NET::

      public DataTable this[string name] { get; }
      public DataTable this[string name, string tableNamespace] { get; }
      public DataTable this[int index] { get; }

* Indexers can be multidimensional (as in the example above).

Operator overloading
--------------------

* Operators can only be defined with the ``static`` keyword::

      public class Point
      {
          ...
          public static Point operator + (Point p1, Point p2)
          {
              return new Point(p1.X + p2.X, p1.Y + p2.Y);
          }
      }

* Parameters don't have to be of the same type::

      public static Point operator + (Point p, int change)
      {
          return new Point(p.X + change, p.Y + change);
      }

      // Also define the same operator with parameters in reverse order

* The += and -= operators (and others) are automatically created
  when you define the related binary operator.

* When overloading ++ and --, pre- and post- is automatically handled.

* Some related operators must be overloaded together, like == and !=.

Custom type conversion
----------------------

* Define an explicit conversion (i.e., when using the () operator)
  within a class definition (in this example, ``Square``)::

      public static explicit operator Square(Rectangle r)
      {
          return new Square(r.Height);
      }

* Can overload with other types::

      public static explicit operator Square(int length)
      {
          return new Square(length);
      }

      public static explicit operator int (Square s)
      {
          return s.Length;
      }

* Define an implicit conversion::

      public static implicit operator Rectangle(Square s)
      {
          return new Rectangle(s.Length, s.Length * 2);
      }

      // Implicit conversion can be used as follows
      Square s = new Square(10);
      Rectangle r = s;

* Cannot define both explicit and implicit conversion operators,
  unless they differ in signature. Defining an implicit conversion operator
  automatically allows user to use an explicit operator.

Extension methods
-----------------

* To extend a type ``Type``, define a ``static`` method in a ``static`` class::

      static class MyExtensions
      {
          public static void ExtensionMethodName(this Type t) { ... }
      }

* The first parameter must always be qualified by ``this``,
  but additional parameters must not.

* Can also extend interfaces.

Anonymous types
---------------

* Create a simple type with automatic (but read-only) properties,
  value-based equality, and ToString() that returns memberwise values::

      var car = new { Make = "BMW", Color = "Black", Speed = 90 };
      Console.WriteLine("You have a {0} {1} going {2} mph",
          car.Color, car.Make, car.Speed);

* The ``GetHashCode()`` implementation uses each annonymous type's members
  to compute a hash value, and the ``Equals()`` implementation also
  uses each annonymous type's members to determine equality. But the ==
  operator tests the reference values, not the type members.

* Separately defined anonymous types with the same member names
  within the same assembly are of the same type.

* An anonymous type can contain another anonymous type.

Pointers
--------

* If using pointers, must compile with "unsafe" option in the compiler.

* Any piece of code that uses pointers, must be used within a special block
  specified by the ``unsafe`` keyword.

* For structures, classes, and members that use pointers,
  must declare as unsafe::

      public struct Node
      {
          public int Value;

          // Struct is safe, but access to these members
          // must occur within an unsafe block
          public unsafe Node* Left;
          public unsafe Node* Right;
      }

* Methods that use pointers must be marked as ``unsafe``::

      unsafe static void SquareIntPointer(int* i)
      {
          *i *= *i;
      }

      // Call within unsafe block
      unsafe
      {
          int i = 5;
          SquareIntPointer(&i)
      }

* Can use ``->`` to access members of a pointer structure.

* Can allocate memory from the call stack::

      char* p = stackalloc char[256];

  Its memory will be deallocated when the method ends.

* Can point to a reference type, but must used the ``fixed`` keyword
  to make sure the garbage collector doesn't take it away::

      PointRef pt = new PointRef();    // Regular class with public members
      fixed (int* p = &pt.x)
      {
          // Use p variable
      }

* The keyword ``sizeof`` can be used to get the size of value types
  (e.g., ``int``, ``short``, and structures).

LINQ (Language Integrated Query)
--------------------------------

* Example: Get items that contain a blank space, and list them
  in alphabetical order::

      IEnumerable<string> subset = from g in currentVideoGames
                                   where g.Contains(" ") orderby g select g;

* In most cases, the return type of a LINQ query is a type implementing
  the ``IEnumerable<T>`` interface, but you can use the ``var`` keyword
  instead::

      var subset = from i in numbers where i < 10 select i;

* LINQ expressions, even if stored in a variable, are not evaluated
  until they are iterated. Therefore, you can iterate over the same
  variable a second time, and it will execute the LINQ expression again.

* To execute a LINQ expression and store it somewhere, you can use
  ``ToArray<>()``, ``ToList<>()``, or ``ToDictionary<,>()``.

* When returning a LINQ query, can return as ``IEnumerable<T>``
  or an array of the type, through the use of ``ToArray()``.

* Can use LINQ on non-generic collections by using ``OfType<>()``,
  which will return an enumeration of objects of the specified type::

      ArrayList myCars = new ArrayList() { ... };
      var myCarsEnum = myCars.OfType<Car>();
      var fastCars = from c in myCarsEnum where c.Speed > 55 select c;

* Can use anonymous types to get multiple things from a query::

      var nameDesc = from p in products select new { p.Name, p.Description };
      foreach (var item in nameDesc)
          // Can use item.Name and item.Description

  But such a query cannot be returned from a method because
  the type of each anonymous method is generated at compile-time.

* There are various LINQ operations: ``Count()``, ``Reverse()``,
  ``Except()`` (difference between containers),
  ``Intersect()`` (common items from containers),
  ``Union()`` (all items from containers, no duplicates),
  ``Concat()`` (concatenation of all items from containers),
  ``Distinct()`` (unique items from a container).

* Aggregation operations: ``Count()``, ``Max()``, ``Min()``, ``Average()``,
  and ``Sum()``.

Object lifetime
---------------

* Garbage collector removes an object from the heap only if it is
  unreachable by any port of the code (incomplete explanation).

* If managed heap does not have more memory to allocate an object,
  a garbage collection will occur.

* During garbage collection, the CLR builds an object graph
  of dependencies, frees unreachable objects, the heap is compacted,
  and references are adjusted to the new heap configuration.

* The CLR uses "generations" to determine which unreachable objects
  need to be removed; result is that newer objects (like local variables)
  are removed more quickly than older objects.

* Two main reasons to manually do garbage collection:

  #. About to enter into code you don't want interrupted by garbage collection
  #. Just finished allocating a large number of objects,
     and want them removed as soon as possible

  To force manual garbage collection::

      GC.Collect();
      GC.WaitForPendingFinalizers();

* System.Object has a virtual method called ``Finalize()`` that is called
  by the garbage collection before removing the object from memory
  (but cannot override this method on structure types).

* The only reason to override ``Finalize()`` is when dealing
  with unmanaged resources.

* When unmanaged resources should be immediately released,
  one can implement the ``IDisposable`` interface,
  and the client should call ``Dispose()`` on that object.
  Therefore, if a class extends ``IDisposable``, call its ``Dispose()`` method
  (though some classes may have a ``Close()`` method).

* To ensure that ``Dispose()`` is called on an object (even in the face
  of an exception), use the ``using`` keyword::

      using (MyResourceWrapper rw = new MyResourceWrapper())
      {
          // Use rw
      }

* Use the disposal pattern to permit desposing of managed and unmanaged
  resources in ``Dispose()`` but only unmanaged resources in ``Finalize()``::

      class MyResourceWrapper : IDisposable
      {
          private bool disposed = false;

          public void Dispose()
          {
              CleanUp(true);
              GC.SuppressFinalize(this);
          }

          private void CleanUp(bool disposing)
          {
              if (!disposed)
              {
                  if (disposing)
                  {
                      // Dispose of managed resources
                  }

                  // Clean up unmanaged resources
              }

              disposed = true;
          }

          ~MyResourcesWrapper()    // Way to implement Finalize()
          {
              CleanUp(true);
          }
      }

* Can use "lazy initialization" of an object, so instead of::

      AllTracks allSongs = new AllTracks()

  one can say::

      Lazy<AllTracks> allSongs = new Lazy<AllTracks>();
      allSongs.Value;    // Causes the actual instantiation

* Can use a lambda expression to send parameters to the constructor
  of ``AllTracks`` above (see p. 499).


Class libraries
---------------

* Can use aliases to both classes and namespaces::

      using bf = System.Runtime.Serialization.Formatters.Binary.BinaryFormatter;
      using bfNamespace = System.Runtime.Serialization.Formatters.Binary;

* Can nest namespaces by defining a namespace within another,
  or simply using the dot notation.

* An assembly is a versioned, self-describing binary file hosted by the CLR
  (as a .dll or .exe file).

* Applications can make use of class libraries stored as .dll's,
  but also as .exe's (though the latter is not very common),

* Private assemblies reside in the same directory (or subdirectory)
  as the client application that uses them. Shared assemblies are libraries
  intended to be consumed by numerous applications on a single machine
  and are deployed to the global assembly cache (GAC).

* When you reference a private assembly using Visual Studio,
  it will be copied to the output directory of the client application.

* Assemblies can be used by applications written in other .NET languages,
  including the use of properties and inheritance.

* When searching for a private assembly, the CLR will search for it
  in the application's directory, but not in subdirectories,
  unless specified in the application's configuration file.

* Visual Studio will automatically copy App.config to the proper
  assembly's name .config file. Add App.config to the project
  by choosing "Application Configuration File" when adding a New Item.

* For .NET 3.5 and earlier, the GAC is located in C:\Windows\assembly.
  For .NET 4.0 and greater, the GAC is located in
  C:\Windows\Microsoft.NET\assembly\GAC_MSIL.

* Before deploying an assembly to the GAC, it must be assigned a strong name,
  which is used to uniquely identify the publisher of a .NET binary.

* To automatically increment the build and revision numbers
  on each compilation, write the following in the AssemblyInfo.cs file::

      [assembly: AssemblyVersion("1.0.*")]

* To create a strong name, which will be assigned to the assembly
  at ever compilation:

  #. Double-click the Properties icon of the Solution Explorer.
  #. Select the Signing tab.
  #. Select the "Sign the assembly" check box.
  #. Choose the <New..> option from the drop-down list.
  #. Provide a name for the .snk file (password-protection is optional).

* To install a strongly named assembly into the GAC::

      cd <path of assembly>
      gacutil -i <name of assembly>.dll

  Check it is installed::

      gatutil -l <name of assembly>

* Can have multiple versions of the same shared assembly.
  Reference to the correct one for a specific client application.

* Can dynamically redirect to a specific version of a shared library
  (see p. 547); can also be done machine-wide (and provide exceptions).

* Assemblies may be loaded from other locations, such as remote locations,
  using the <codeBase> element, but these assemblies must be strongly named.

* Can story application-specific configuration settings in App.config
  and use classes in System.Configuration to read them.

Type reflection, late binding, and attributes
---------------------------------------------

* An assembly's metadata describes both the set of internal types
  and any external types that the internal types reference.

* ``System.Type`` contains many properties and methods that allow one
  to obtain information about a type (e.g., is it abstract).

* Can obtain a ``Type`` object in several ways::

      // SportsCar is known at compile-time and sc is an existing object
      SportsCar sc = new SportsCar();
      Type t1 = sc.GetType();

      // SportsCar is known at compile-time, but no object needed
      Type t2 = typeof(SportsCar);

      // SportsCar is not known at compile-time, is in internal assembly
      Type t3 = Type.GetType("CarLibrary.SportsCar");

      // SportsCar is not known at compile-time, is in external assembly
      Type t4 = Type.GetType("CarLibrary.SportsCar, CarLibrary");

      // SpyOptions is a nested type (e.g., an enumeration)
      Type t5 = Type.GetType("CarLibrary.JamesBondCar+SpyOptions");

* Generic type names are specified by the name of the type,
  followed by a back tick character, then followed by the number
  of type parameters::

      System.Collections.Generic.List`1
      System.Collections.Generic.Dictionary`2

* Can load an assembly with ``Assembly.Load()`` or ``Assembly.LoadFrom()``.
  The first expects the friendly name (i.e., without ``.dll``)
  and the second expects a full path of the assembly (with ``.dll``).

* Get all types from an assembly with the ``GetTypes()`` method
  of an ``Assembly`` object.

* To load a shared assembly (i.e., one in the GAC), one must provide
  a public key token when loading the assembly.

* After loading an assembly, can create an object in that assembly
  using the ``Activator`` class::

      // asm is of type Assembly
      Type miniVan = asm.GetType("CarLibrary.MiniVan");
      object obj = Activator.CreateInstance(miniVan);

* Call a method with no parameters of a late-bound object
  using ``Invoke`` from ``MethodInfo``::

      MethodInfo mi = miniVan.GetMethod("TurboBoost");
      mi.Invoke(obj, null);    // obj from above

* Call a method with parameters by specifying an array of ``objects``::

      MethodInfo mi = miniVan.GetMethod("TurnOnRadio");
      mi.Invoke(obj, new object[] { true, 2 });

* Create a custom attribute by deriving from ``System.Attribute``
  (mark the new class as ``sealed``).

* To restrict use of a custom attribute to a class or structure::

      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct)]
      public sealed class VehicleDescriptionAttribute : System.Attribute
      {
          ...
      }

* Get the attributes of a type (the boolean controls whether to search
  up the inheritance chain)::

      Type t = typeof(Winnebago);
      object[] customAtts = t.GetCustomAttributes(false);

* To get attributes of a specific type::

      // Here attributeString is the full name of the attribute type
      Type attr = asm.GetType(attributeString);
      object[] atts = t.GetCustomAttributes(attr, false);

* To get the property of an attribute::

      // Here "Description" is the name of a property
      PropertyInfo propDesc = attr.GetProperty("Description");
      propDesc.GetValue(attrib, null);  // atttrib is a specific attribute obj

Dynamic types
-------------

* Declare and define a dynamic type
  (the same variable can be assigned to a different type)::

      dynamic t = "Hello!";
      t = false;
      t = new List<int>();

* There is no type-checking by the compiler with dynamic types.

* The ``dynamic`` keyword may be used on fields, properties,
  method return types, and parameter types.

* Methods on dynamic data cannot use lambdas::

      dynamic a = GetDynamicObject();
      a.Method(arg => Console.WriteLine(arg)); // Error

* Dynamic types cannot understand any extension methods (including LINQ)::

      dynamic a = GetDynamicObject();
      var data = from d in a select d; // Error

* Simplifies late-binding::

      Type miniVan = asm.GetType("CarLibrary.MiniVan");
      dynamic obj = Activator.CreateInstance(miniVan);
      obj.TurboBoost(10); // Parameters can be sent to method directly

Multithreaded, parallel, and async programming
----------------------------------------------

Multithreading
..............

* Get the current thread (using ``System.Threading`` namespace)::

      Thread currThread = Thread.CurrentThread;

* Get the AppDomain of the current thread::

      AppDomain ad = Thread.GetDomain();

* Invoking a delegate, calls its registered methods synchronously.

* Delegates are processed by the compiler, which generates
  a new class definition, which includes the ``Invoke`` method
  (synchronous) as well as ``BeginInvoke`` and ``EndInvoke``
  (used to call the delegate methods asynchronously).

* For example, given a method ``int Add(int x, int y)``
  and a delegate ``delegate int BinaryOp(int x, int y)``,
  call the ``Add`` method asynchronously as follows::

      BinaryOp b = new BinaryOp(Add);
      IAsyncResult iftAR = b.BeginInvoke(10, 10, null, null)
      ...
      int answer = b.EndInvoke(iftAR);    // fetch return value

* But, in the above, if what's in ``...`` doesn't take too long,
  then calling ``EndInvoke`` effectively blocks the main thread.
  One can tell whether an asynchronously called method has finished
  by using the ``IsCompleted`` property on an ``IAsyncResult``.

* Can have a method to be called when the async task is completed.
  Specify this method in ``BeginInvoke`` (set to ``null`` above)::

      BinaryOp b = new BinaryOp(Add);
      IAsyncResult iftAR = b.BeginInvoke(10, 10,
          new AsyncCallback(AddComplete), null);
      ...
      static void AddComplete(IAsyncResult iftAR)
      {
          // Code runs in secondary thread (not in primary thread)
      }

* To get the result of the asynchronous method within the callback::

      // import System.Runtime.Remoting.Messaging
      static void AddComplete(IAsyncResult iftAR)
      {
          AsyncResult ar = (AsyncResult)iftAR;
          BinaryOp b = (BinaryOp)ar.AsyncDelegate;
          int result = b.EndInvoke(iftAR);
      }

* Can pass arbitrary data to the callback method through ``BeginInvoke``::

      IAsyncResult iftAR = b.BeginInvoke(10, 10,
          new AsyncCallback(AddComplete), "Blah");
      ...
      static void AddComplete(IAsyncResult iftAR)
      {
          string msg = (string)iftAR.AsyncState;
      }

* Create and start a new thread manually::

      // Create the method that will be run in a thread;
      public class Printer
      {
          // For now, method takes no parameters and returns void
          public void PrintNumbers()
          {
              ...
          }
      }

      static void Main()
      {
          Printer p = new Printer();

          // Create the thread and start it
          Thread thread = new Thread(new ThreadState(p.PrintNumbers));
          thread.Name = "Secondary";
          thread.Start();
      }

* Instead of ``ThreadStart``, use ``ParameterizedThreadStart``
  if you want to pass in an ``object`` parameter to the method.

* One thread-safe way to force a thread to wait until another is done,
  is to use the ``AutoResetEvent`` class::

      // Send false to signify you have not yet been notified that is not done
      private static AutoResetEvent waitHandle = new AutoResetEvent(false);

      static void Main(string[] args)
      {
          AddParams ap = new AddParams(10, 10);
          Thread t = new Thread(new ParameterizedThreadStart(Add));
          t.Start(ap);

          // Wait until notified
          waitHandle.WaitOne();
      }

      static void Add(object data)
      {
          ...

          // Tell other thread we are done
          waitHandle.Set();
      }

* Foreground threads can prevent the current application from terminaning.
  The CLR will not shut down an application until all foreground threads
  have ended. In contrast, background threads can be terminated by the CLR
  if all foreground threads have terminated.

* By default, threads created by ``Thread.Start()`` are foreground threads
  (i.e., the AppDomain will not unload until it has completed its work).

* To set a thread as a background thread::

      thread.IsBackground = true;
      thread.Start();

* To prevent other threads from accessing the same scope of a method,
  use a lock scope (``this`` for private methods, an object for public)::

      private void SomePrivateMethod()
      {
          lock (this)
          {
              // Thread safe
          }
      }

      public class Printer
      {
          private object threadLock = new object();

          public void PrintNumbers()
          {
              lock (threadLock)
              {
                  ...
              }
          }
      }

* You have more control over synchronization with the ``Monitor`` class.

* The ``System.Threading.Interlocked`` type provides a set of static
  methods to perform some atomic operations
  (e.g., ``Increment()``, ``Decrement()``, ``Exchange()``).

* To make all methods of a class thread-safe, use the
  ``[Synchronization]`` attribute on the class and have it derive
  from ``ContextBoundObject``.

* Run a method using a timer::

      class Program
      {
          // Method to run with a timer (one parameter)
          static void PrintTime(object state)
          {
              ...
          }

          static void Main(string[] args)
          {
              // Create the delegate for the Timer
              TimerCallback timeCB = new TimerCallback(PrintTime);

              Timer t = new Timer(
                  timeCB,    // the delegate created above
                  null,      // parameter passed as "state" above
                  0,         // time to wait before starting (in ms)
                  1000);     // time between calls (in ms)
          }
      }

* Instead of creating a new thread manually, you can use the ``ThreadPool``.
  This is a pool of worker threads (run in the background) maintained
  by the runtime. It's more efficient than creating threads::

      Printer p = new Printer();    // Contains method to run in the background

      // Create delegate object
      WaitCallback workItem = new WaitCallback(PrintTheNumbers);

      // Queue the method ten times
      for (int i = 0; i < 10; i++)
      {
          // Queue workItem and sent parameter p
          ThreadPool.QueueUserWorkItem(workItem, p);
      }
      ...
      static void PrintTheNumbers(object state)
      {
          // state is the p variable sent above
          Printer task = (Printer)state;
          task.PrintNumbers();
      }

Task Parallel Library (TPL)
...........................

* Process a loop in as many CPUs as possible (will block current thread
  until the loop is done)::

      Parallel.ForEach(list, item =>
      {
          // Perform task here
      });

* Secondary threads should not "touch" GUI controls that it did not create,
  but can do so by calling::

      // Windows Forms API; use this.Dispatcher.Invoke for WPF
      this.Invoke((Action)delegate
      {
          // Access GUI controls
      });

* Use the ``Task`` class to invoke a method on a secondary thread::

      Task.Factory.StartNew(() =>
      {
          // Do task here
      });

* A cancellation request can be provided when using methods on ``Parallel``.
  ``Parallel.For`` and ``Parallel.ForEach`` can be passed in a
  ``ParallelOptions`` object, which contains a ``CancellationTokenSource``
  object::

      // Class-level variable
      private CancellationTokenSource cancelToken =
          new CancellationTokenSource();

      // Cancel button handler
      private void btnCancel_Click(object sender, EventArgs e)
      {
          cancelToken.Cancel();
      }

      // Method to use Parallel.ForEach()
      private void ProcessFiles()
      {
          // Create a ParallelOptions object
          ParallelOptions parOpts = new ParallelOptions();
          parOpts.CancellationToken = cancelToken.Token;
          parOpts.MaxDegreeOfParallelism = System.Environment.ProcessorCount;

          try
          {
              Parallel.ForEach(files, parOpts, currentFile =>
              {
                  parOpts.CancellationToken.ThrowIfCancellationRequestion();

                  // Do work here
              });
          }
          catch (OperationCanceledException ex)
          {
              // Handle cancellation
          }
      }

* Can perform separate tasks in parallel using ``Parallel.Invoke``::

      Parallel.Invoke(
          () =>
          {
              // Task 1
          },
          () =>
          {
              // Task 2
          },
          () =>
          {
              // Task 3, and so on...
          });

* Use ``AsParallel`` (PLINQ query) to execute a LINQ query in parallel::

      from num in source.AsParallel() where num % 3 == 0;

* Can use cancellation requests::

      // Class-level object
      private CancellationTokenSource cancelToken =
          new CancellationTokenSource();

      // Cancel button handler
      private void btnCancel_Click(...)
      {
          cancelToken.Cancel();
      }

      // PLINQ query with cancellation
      try
      {
          modThreeIsZero = (from num in
              source.AsParallel().WithCancellation(cancelToken.Token)
              where num % 3 == 0 orderby num descending select num).ToArray();
      }
      catch (OperationCanceledException ex)
      {
          // Handle cancellation
      }

Asynchronous calls
..................

* Use ``async`` and ``await`` together::

      // Button handler, called asynchronously
      private async void btnCallMethod_Click(...)
      {
          // Call DoWork and wait until done
          this.Text = await DoWorkAsync();
      }

      private Task<string> DoWorkAsync()
      {
          return Task.Run(() =>
          {
             // Do work
             return "Return value";
          });
      }

* Use nongeneric ``Task`` to omit a return value::

      private Task DoWorkAsync()
      {
          Task.Run(() => { // Do work });
      }

      // Caller
      private async void handler(...)
      {
          await DoWorkAsync();
      }

* An async method can contain multiple await methods,
  and the tasks can be inlined without having to create external methods::

      private async void handler(...)
      {
          await Task.Run(() => { // Do something });
          await Task.Run(() => { // Do something else });
          ...
      }
