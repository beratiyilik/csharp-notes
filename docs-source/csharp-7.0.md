# C# 7.0 New Features Guide

## Table of Contents

- [C# 7.0 New Features Guide](#c-70-new-features-guide)
  - [Table of Contents](#table-of-contents)
  - [Introduction and Overview](#introduction-and-overview)
  - [New Features](#new-features)
    - [Tuples and Deconstruction](#tuples-and-deconstruction)
    - [Pattern Matching](#pattern-matching)
      - [Constant Patterns](#constant-patterns)
      - [Type Patterns](#type-patterns)
      - [Var Patterns](#var-patterns)
      - [A Comprehensive Example](#a-comprehensive-example)
    - [Local Functions](#local-functions)
    - [Ref Returns and Locals](#ref-returns-and-locals)
    - [Out Variables](#out-variables)
    - [Literal Improvements](#literal-improvements)
      - [Binary Literals](#binary-literals)
      - [Digit Separators](#digit-separators)
    - [Generalized Async Return Types](#generalized-async-return-types)
    - [Expression Bodied Members Expansion](#expression-bodied-members-expansion)
    - [Throw Expressions](#throw-expressions)
  - [Conclusion](#conclusion)

## Introduction and Overview

C# 7.0 brought many improvements that make coding easier and more efficient. Features like Tuples let you return multiple values from functions, and Pattern Matching offers smarter ways to handle data. Local Functions enable more organized code, while Ref Returns improve performance. Out Variables, Literal Improvements, and Expanded Expression Bodied Members simplify syntax. Finally, Generalized Async Return Types and Throw Expressions enhance flexibility in handling operations and errors.

## New Features

### Tuples and Deconstruction

- **Tuples** are a feature in C# that allows you to store multiple values within a single object. Think of them like a container where you can keep various items, but unlike a box, you can access each item directly by its position or a name if you've given one. This can be incredibly handy when you want to return more than one value from a method without creating a specific class or structure for that purpose.
- **Defining and using a Tuple**

  ```csharp
  (string, int, bool) GetPersonDetails()
  {
    // Assume we fetch these from a database or some source
    string name = "Alex";
    int age = 30;
    bool hasPet = true;

    // Return all three values as a tuple
    return (name, age, hasPet);
  }
  ```

- In the above example, `GetPersonDetails` returns a tuple containing a `string`, an `int`, and a `bool`. You can access each value by its position, like so:

  ```csharp
  var personDetails = GetPersonDetails();
  Console.WriteLine($"Name: {personDetails.Item1}, Age: {personDetails.Item2}, HasPet: {personDetails.Item3}");
  ```

- However, referring to tuple elements as `Item1`, `Item2`, etc., is not very readable. **Named Tuples** provide a more descriptive way to work with tuples by giving each element a name. Here's how you can define a named tuple:

  ```csharp
  (string Name, int Age, bool HasPet) GetPersonDetails()
  {
      // Assume we fetch these from a database or some source
      string name = "Alex";
      int age = 30;
      bool hasPet = true;
      // Return all three values as a tuple
      return (name, age, hasPet);
  }

  var personDetails = GetPersonDetails();
  Console.WriteLine($"Name: {personDetails.Name}, Age: {personDetails.Age}, HasPet: {personDetails.HasPet}");
  ```

- **Deconstruction** is a feature that allows you to break a tuple into its individual components and assign them to separate variables. This can be done in a single line of code, making it easy to work with the values stored in a tuple.

  ```csharp
  var (string name, int age, bool hasPet) = GetPersonDetails();
  Console.WriteLine($"Name: {name}, Age: {age}, HasPet: {hasPet}");
  ```

- In the above example, the tuple returned by `GetPersonDetails` is deconstructed into three separate variables: `name`, `age`, and `hasPet`. You can then use these variables as needed.

- **Discards** are placeholders for tuple elements that you want to ignore. If you're only interested in some of the values returned by a method, you can use discards to indicate that you don't care about the others.

  ```csharp
  var (_, age, _) = GetPersonDetails();
  Console.WriteLine($"Age: {age}");
  ```

- **Comprehensive Example**

  ```csharp
  public class PersonManager
  {
      public (string Name, int Age, bool HasPet) GetPersonDetails(int personId)
      {
          // In a real application, you'd fetch these details from a database
          return ("Alex", 30, true);
      }

      public void DisplayPersonDetails(int personId)
      {
          var (name, age, hasPet) = GetPersonDetails(personId);
          Console.WriteLine($"Name: {name}, Age: {age}, Has Pet: {hasPet}");
      }
  }

  class Program
  {
      static void Main(string[] args)
      {
          var personManager = new PersonManager();
          personManager.DisplayPersonDetails(1);
      }
  }
  ```

- In this example, PersonManager has a method GetPersonDetails that returns a tuple with person details. The DisplayPersonDetails method deconstructs this tuple into individual variables and prints them. This showcases how tuples and deconstruction can simplify returning and working with multiple values in C#.

### Pattern Matching

- Pattern matching in C# 7.0 is a powerful feature that allows you to write more concise and readable code. It enables you to check the type of an object and its properties in a more fluid and intuitive way. C# 7.0 introduces three types of patterns: constant patterns, type patterns, and var patterns. Let's delve into each of these with some examples to help you understand how they work.

#### Constant Patterns

- These patterns allow you to check if a value is equal to a specific constant. This can be useful when you want to perform different actions based on the value of a variable.

  ```csharp
  object value = "Hello World";
  if (value is "Hello World") // Constant pattern
  {
      Console.WriteLine("The value is 'Hello World'");
  }
  ```

- In the above example, the constant pattern checks if the value of the object is equal to "Hello World" and executes the corresponding code block if the condition is true.

#### Type Patterns

- Type patterns allow you to check if an object is of a specific type and optionally cast it to that type in a single operation. This can help you avoid writing multiple if-else blocks to handle different types of objects.

  ```csharp
  object obj = 123;
  if (obj is int number) // Type pattern
  {
      Console.WriteLine($"The number is {number}");
  }
  ```

- Here, the type pattern checks if obj is an int and directly assigns its value to the variable number if it is. This eliminates the need for separate type checking and casting operations.

#### Var Patterns

- The var pattern always matches the object it's compared to and assigns it to a variable. This might seem unnecessary at first glance, as it always succeeds, but it can be useful in switch statements to catch all cases not covered by other patterns.

  ```csharp
  object anything = new object();
  if (anything is var thing) // Var pattern
  {
      Console.WriteLine($"Found an object of type {thing.GetType().Name}");
  }
  ```

- Even though the var pattern will always succeed, it's useful for assigning the matched object to a variable (`thing` in this case) for further operations.

#### A Comprehensive Example

- Let's put all these patterns together in a more comprehensive example to demonstrate how they can be used in different scenarios.

  ```csharp
  public abstract class Shape
  {
    public abstract double Area();
  }

  public class Circle : Shape
  {
    public double Radius { get; }

    public Circle(double radius) => Radius = radius;

    public override double Area() => Math.PI * Math.Pow(Radius, 2);
  }

  public class Rectangle : Shape
  {
    public double Length { get; }
    public double Width { get; }

    public Rectangle(double length, double width)
    {
        Length = length;
        Width = width;
    }

    public override double Area() => Length * Width;
  }

  public class Triangle : Shape
  {
    public double Base { get; }
    public double Height { get; }

    public Triangle(double @base, double height)
    {
        Base = @base;
        Height = height;
    }

    public override double Area() => 0.5 * Base * Height;
  }

  public static void PrintCircleArea(Shape shape)
  {
    if (shape is Circle c)
    {
        Console.WriteLine($"Circle with radius {c.Radius} has an area of {c.Area():F2}.");
    }
    else
    {
        Console.WriteLine("The shape is not a circle.");
    }
  }

  public static void PrintShapeArea(Shape shape)
  {
    switch (shape)
    {
        case Circle c:
            Console.WriteLine($"Circle with radius {c.Radius} has an area of {c.Area():F2}.");
            break;
        case Rectangle r when r.Length == r.Width:
            Console.WriteLine($"Square with side {r.Length} has an area of {r.Area():F2}.");
            break;
        case Rectangle r:
            Console.WriteLine($"Rectangle with length {r.Length} and width {r.Width} has an area of {r.Area():F2}.");
            break;
        case Triangle t:
            Console.WriteLine($"Triangle with base {t.Base} and height {t.Height} has an area of {t.Area():F2}.");
            break;
        default:
            Console.WriteLine("Unknown shape.");
            break;
    }
  }
  ```

### Local Functions

- Local functions in C# 7.0 are a way to define methods inside other methods. These functions are useful when a piece of code is only relevant within the scope of a single method and nowhere else in your class. They help keep your code organized and more readable by hiding helper methods that are only used by a single method.
- **Encapsulation**: They encapsulate a piece of functionality without exposing it outside the containing method.
- **Readability**: They can make the intent of your code clearer, as the helper function is placed close to where it's used.
- **Variables Scope**: They can access variables from the enclosing method, making it easier to work with those variables without passing them as parameters.

  ```csharp
  public class FibonacciExample
  {
      public static void Main(string[] args)
      {
          Console.WriteLine("Enter the position of the Fibonacci sequence:");
          int position = Convert.ToInt32(Console.ReadLine());

          // Calculate and print the Fibonacci number
          Console.WriteLine($"Fibonacci number at position {position} is {Fibonacci(position)}");
      }

      public static int Fibonacci(int n)
      {
          // Check for valid input
          if (n < 0)
          {
              throw new ArgumentException("The position must be a non-negative integer", nameof(n));
          }

          // Local function to calculate Fibonacci
          int Fib(int number)
          {
              if (number <= 1) return number;
              return Fib(number - 1) + Fib(number - 2);
          }

          // Use the local function
          return Fib(n);
      }
  }
  ```

### Ref Returns and Locals

- Ref Returns and Locals is a feature introduced in C# 7.0 that allows methods to return references to variables, rather than the values themselves. This feature is particularly useful for working with large data structures, where passing around copies of data can be expensive in terms of performance. By returning a reference, you can directly access and modify the original data, which can lead to more efficient code.
- **Ref Returns**: A method can return a reference to a variable rather than the value. This means that any changes made to this reference are reflected in the original data structure.
- **Ref Locals**: You can store the returned reference in a local variable. This local variable now refers directly to the original data, allowing for efficient modifications.

- **Working with Arrays**

  ```csharp
  public class RefReturnsExample
  {
      public static void Main(string[] args)
      {
          int[] numbers = { 1, 2, 3, 4, 5 };

          ref int number = ref Find(3, numbers);
          number = 10;

          Console.WriteLine(string.Join(", ", numbers)); // Output: 1, 2, 10, 4, 5
      }

      public static ref int Find(int target, int[] numbers)
      {
          for (int i = 0; i < numbers.Length; i++)
          {
              if (numbers[i] == target)
              {
                  return ref numbers[i];
              }
          }

          throw new InvalidOperationException("Number not found");
      }
  }
  ```

- **Avoiding Unnecessary Copies**

  ```csharp
    public struct GameEntity
    {
        public int Health;
          // Other properties...
    }
    public class Game
    {
        GameEntity[] entities = new GameEntity[1000];
        public ref GameEntity FindEntity(int index)
        {
            // Safety checks should be added in real scenarios
            return ref entities[index];
        }
        public void UpdateEntityHealth(int index, int newHealth)
        {
            ref GameEntity entity = ref FindEntity(index);
              entity.Health = newHealth;
        }
    }
  ```

- In this example, `FindEntity` returns a reference to a `GameEntity` within the array. This allows `UpdateEntityHealth` to modify the entity's health directly in the array without copying the entire structure.

### Out Variables

- The "Out Variables" feature introduced in C# 7.0 simplifies how variables are declared when they are to be used with methods that have output parameters. Before C# 7.0, if you wanted to use an output parameter in a method, you had to declare the variable in a separate statement before you could pass it to the method. This often led to more verbose code, especially when the variable was only needed for the output of the method call.
- **Old Way**

  ```csharp
    int result; // Pre-declaration
    bool resultParsed = int.TryParse("123", out result); // Method call
    if(resultParsed)
    {
        Console.WriteLine($"Parsed value: {result}"); // Use the result
    }
  ```

- **New Way**: C# 7.0 introduced a more streamlined way to handle this by allowing you to declare out variables inline, at the point where they are passed as out arguments. This reduces the verbosity of the code and makes it clearer. In this improved approach, `number` is declared directly within the call to `TryParse`. This makes the code cleaner and focuses on where the variable is actually used.

  ```csharp
    if(int.TryParse("123", out int result)) // Declare and use the variable in one line
    {
        Console.WriteLine($"Parsed value: {result}"); // Use the result
    }
  ```

### Literal Improvements

#### Binary Literals

- **Old Way**

  ```csharp
  int permission = 0x4; // 4 in hexadecimal is 100 in binary.
  ```

- **New Way**:
  You can now define binary literals using the `0b` prefix, making it easier to work with binary numbers.

  ```csharp
  int permission = 0b0100; // Represents 4 in binary directly.
  ```

#### Digit Separators

- **Old Way**

  ```csharp
  int billion = 1000000000;
  long creditCardNumber = 1234567890123456;
  ```

- **New Way**: You can now use underscores `_` as digit separators to make large numbers more readable.

  ```csharp
  int billion = 1_000_000_000;
  long creditCardNumber = 1234_5678_9012_3456;
  ```

- And with binary literals, you can combine digit separators and binary literals for even more readable code. In this example, `permissions` might represent a set of boolean flags where each bit is a different permission setting. Using the digit separator makes it easier to see the individual groups of bits.

  ```csharp
  int permissions = 0b0010_1010; // Binary representation of 42 with digit separators.
  ```

### Generalized Async Return Types

- C# 7.0 introduced a feature called Generalized Async Return Types, which allows you to return any type from an async method. Before C# 7.0, async methods could only return `Task`, `Task<T>`, or `void`. With this new feature, you can return any type that has a suitable `GetAwaiter` method. This can be useful when you want to return a custom type or a value directly from an async method without wrapping it in a `Task`.
- **Old Way**

  ```csharp
  public async Task<string> GetDataAsync()
  {
        // Assume this method might complete synchronously sometimes.
        return await Task.FromResult("Hello, World!");
  }
  ```

- **New Way**: With Generalized Async Return Types, you can return a string directly without wrapping it in a `Task`.

  ```csharp
  public async ValueTask<string> GetDataAsync()
  {
        // Assume this method might complete synchronously sometimes.
        return "Hello, World!";
  }
  ```

- In scenarios where `GetDataAsync` can complete synchronously and return its result immediately, using `ValueTask<string>` avoids allocating a Task object, thus reducing the memory footprint and potentially improving the application's performance.

### Expression Bodied Members Expansion

- **Old Way**

  ```csharp
  public class Person
  {
      private string name;
      public Person(string name)
      {
          this.name = name;
      }
      public string Name
      {
          get { return name; }
          set { name = value; }
      }
  }
  ```

- **New Way**: C# 7.0 introduced an expansion of expression-bodied members, allowing you to use expression bodies for methods, properties, and other members. This can make your code more concise and readable.

  ```csharp
    public class Person
    {
        private string name;
        public Person(string name) => this.name = name;
        public string Name
        {
            get => name;
            set => name = value;
        }
    }
  ```

- Constructors and Finalizers

  ```csharp
    class FileLogger
    {
        private readonly string logPath;
        // Constructor
        public FileLogger(string path) => logPath = path;
        // Finalizer
        ~FileLogger() => Console.WriteLine("Finalizer executed.");
    }
  ```

- Read and Write Properties

  ```csharp
    class Temperature
    {
        private double celsius;

        // Property with expression-bodied getter and setter
        public double Celsius
        {
            get => celsius;
            set => celsius = value;
        }

        // Expression-bodied read-only property
        public double Fahrenheit
        {
            get => celsius * 9 / 5 + 32;
        }
    }
  ```

- Indexers

  ```csharp
    class DataStore
    {
        private int[] data = new int[10];

        // Expression-bodied indexer
        public int this[int index]
        {
            get => data[index];
            set => data[index] = value;
        }
      }
  ```

### Throw Expressions

- **Old Way**

  ```csharp
  public int Divide(int x, int y)
  {
      if (y == 0)
      {
          throw new DivideByZeroException();
      }
      return x / y;
  }
  ```

- **New Way**: C# 7.0 introduced throw expressions, which allow you to throw exceptions in a more concise way. This can be useful when you want to throw an exception based on a condition within an expression.

  ```csharp
    public int Divide(int x, int y) => y != 0 ? x / y : throw new DivideByZeroException();
  ```

## Conclusion

C# 7.0 introduces powerful features that enhance code readability, efficiency, and maintainability. With additions like Tuples for multi-value returns, Pattern Matching for data inspection, and Local Functions for scoped method definitions, developers can write more expressive and concise code. Improvements like Ref Returns and Locals, Out Variables, and Literal Enhancements further streamline coding practices. Generalized Async Return Types expand asynchronous programming capabilities, while expanded Expression Bodied Members and Throw Expressions simplify syntax for a variety of use cases. These features collectively make C# more versatile and enjoyable for developers, paving the way for writing robust and high-performing applications.

[Previous: C# 6.0 New Features Guide](./csharp-6.0.md) | [Back to main page](../README.md) | [Next: C# 8.0 New Features Guide](./csharp-8.0.md)
