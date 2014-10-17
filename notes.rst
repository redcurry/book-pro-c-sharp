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


