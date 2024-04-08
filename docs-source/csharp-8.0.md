# C# 8.0 New Features Guide

## Table of Contents

- [C# 8.0 New Features Guide](./csharp-8.0.md)
  - [Table of Contents](./csharp-8.0.md#table-of-contents)
  - [Introduction and Overview](./csharp-8.0.md#introduction-and-overview)
  - [New Features](./csharp-8.0.md#new-features)
    - [Readonly Members](./csharp-8.0.md#readonly-members)
    - [Default Interface Methods](./csharp-8.0.md#default-interface-methods)
    - [Pattern Matching Enhancements](./csharp-8.0.md#pattern-matching-enhancements)
    - [Using Declarations](./csharp-8.0.md#using-declarations)
    - [Static Local Functions](./csharp-8.0.md#static-local-functions)
    - [Disposable Ref Structs](./csharp-8.0.md#disposable-ref-structs)
    - [Nullable Reference Types](./csharp-8.0.md#nullable-reference-types)
    - [Asynchronous Streams](./csharp-8.0.md#asynchronous-streams)
    - [Indices and Ranges](./csharp-8.0.md#indices-and-ranges)
    - [Null-Coalescing Assignment](./csharp-8.0.md#null-coalescing-assignment)
    - [Unmanaged Constructed Types](./csharp-8.0.md#unmanaged-constructed-types)
    - [Stackalloc in Nested Expressions](./csharp-8.0.md#stackalloc-in-nested-expressions)
    - [Enhancement of Interpolated Verbatim Strings](./csharp-8.0.md#enhancement-of-interpolated-verbatim-strings)
    - [Target-Typed New Expressions](./csharp-8.0.md#target-typed-new-expressions)
  - [Conclusion](./csharp-8.0.md#conclusion)

## Introduction and Overview

C# 8.0, released in September 2019, is a major update that brings several new features aimed at making coding in C# more efficient, safe, and expressive. This version is the first to specifically target .NET Core, introducing changes that enhance the language's capabilities significantly. Some of the key new features include:

- **Readonly Members**: Enhancements that allow struct members to be marked as `readonly`, indicating that they do not modify the state of the struct.

- **Default Interface Methods**: Support for adding new methods to interfaces with default implementations, helping to maintain compatibility with existing code.

- **Pattern Matching Enhancements**: Extended pattern matching capabilities for more expressive and concise code.

- **Using Declarations**: A more streamlined way to ensure unmanaged resources are disposed of properly.

- **Static Local Functions**: Local functions can now be declared static, clarifying that they don't capture any variables from the enclosing scope.

- **Disposable Ref Structs**: Structures that use the `ref` keyword can now implement the `IDisposable` interface, allowing for more efficient resource management.

- **Nullable Reference Types**: Introduction of syntax that enables developers to explicitly declare when a reference type is allowed to be null, reducing the chances of null reference exceptions.

- **Asynchronous Streams**: Support for `IAsyncEnumerable<T>`, making it easier to work with asynchronous streams of data.

- **Indices and Ranges**: New syntax for more easily and efficiently accessing elements and ranges of elements in collections.

- **Null-Coalescing Assignment**: An operator that simplifies the assignment of values to variables if they are currently null.

- **Unmanaged Constructed Types**: Enhanced support for working with unmanaged types, including constructed types like generic structs.

C# 8.0's improvements are designed to improve code safety, reduce boilerplate code, and make asynchronous programming more intuitive. This guide aims to explore these features in detail, providing examples and insights into how they can be used to write better, more reliable C# code.

## New Features

### Readonly Members

- The "Readonly Members" feature in C# 8.0 allows members of a struct to be marked as `readonly`. This means that these members cannot modify the state of the struct. It is particularly useful for ensuring that certain operations on struct instances do not inadvertently change their state, which can be important for maintaining immutability in parts of your code where it's needed.

This feature helps in making the design intent clearer and the code more maintainable by enforcing immutability at the member level, not just at the struct level. This can also potentially improve performance in certain scenarios, as the compiler can make optimizations knowing that these members won't change the state of the struct.

- **Using Readonly Members in Structs**: You can mark fields, properties, and auto-implemented properties as `readonly` in a struct. Here's an example:

  ```csharp
      public struct Point
      {
          public double X { get; }
          public double Y { get; }

          // Constructor
          public Point(double x, double y) => (X, Y) = (x, y);

          // Readonly member method
          // This method does not modify the state of the struct.
          public readonly double Distance => Math.Sqrt(X * X + Y * Y);

          // Trying to modify the state in a readonly method would result in         a compile-time error.
          // Uncommenting the following method will produce a compilation         error.
          /*
          public readonly void Translate(double dx, double dy)
          {
              X += dx; // Error: Cannot assign to 'X' because it is a         readonly instance member
              Y += dy; // Error: Cannot assign to 'Y' because it is a         readonly instance member
          }
          */
      }
  ```

- **Ensuring Immutability with Readonly Members**: By marking members as `readonly`, you can ensure that they don't modify the state of the struct. This can be useful for enforcing immutability in certain parts of your codebase.

  ```csharp
    public struct ReadOnlyVector
    {
        public readonly double X;
        public readonly double Y;

        // Constructor
        public ReadOnlyVector(double x, double y) => (X, Y) = (x, y);

        // Readonly property
        public readonly double Magnitude => Math.Sqrt(X * X + Y * Y);

        // Methods that attempt to modify readonly fields will result in     compilation errors.
    }
  ```

### Default Interface Methods

- In C# 8.0, a notable new feature called "Default Interface Methods" was introduced. This feature allows developers to add new methods to an interface without breaking existing implementations of that interface. Essentially, it enables the evolution of interfaces in a backward-compatible way. If a class that implements the interface doesn't provide its own implementation for the newly added methods, the default implementation specified in the interface will be used instead. This solves a common problem in software development where adding methods to an interface after it has been released would require all implementing classes to be updated. With Default Interface Methods, interfaces are more flexible and can evolve over time without affecting existing codebases

- **Basic Implementation**

  ```csharp
    // Define an interface with a default method
    interface ILogger
    {
        void Log(string message); // Abstract method, must be implemented by the inheriting class

        // Default method, can be overridden by the inheriting class but it's not mandatory
        void LogWarning(string message) => Console.WriteLine($"WARNING: {message}");
    }

    // Implementing the interface without overriding the default method
    class ConsoleLogger : ILogger
    {
        public void Log(string message)
        {
            Console.WriteLine(message);
        }

        // LogWarning uses the default implementation provided by ILogger
    }

    // Usage
    var logger = new ConsoleLogger();
    logger.Log("This is an info message."); // Implemented in ConsoleLogger
    logger.LogWarning("This is a warning message."); // Uses default implementation from ILogger
  ```

- **Overriding the Default Method**

  ```csharp
      // Another implementation of ILogger, this time overriding the default method
      class FileLogger : ILogger
      {
          public void Log(string message)
          {
              // Code to log message to a file
              File.WriteAllText("log.txt", message);
          }

          // Overriding the default LogWarning method
          public void LogWarning(string message)
          {
              // Code to log warning to a file, possibly in a different format
              File.WriteAllText("log.txt", $"[WARNING] {message}");
          }
      }

      // Usage
      var fileLogger = new FileLogger();
      fileLogger.Log("This is an info message."); // Implemented in FileLogger
      fileLogger.LogWarning("This is a warning message."); // Overridden implementation in FileLogger
  ```

- **Adding New Methods to Interface Without Breaking Existing Implementations**

  ```csharp
        // Updated ILogger interface with a new default method
        interface ILogger
        {
            void Log(string message);
            void LogWarning(string message) => Console.WriteLine($"WARNING: {message}");

            // New default method added after some implementations already exist
            void LogError(string message) => Console.WriteLine($"ERROR: {message}");
        }

        // Existing implementation, doesn't need to change or be recompiled to accommodate the new method
        class ConsoleLogger : ILogger
        {
            public void Log(string message)
            {
                Console.WriteLine(message);
            }

            // LogWarning and LogError use the default implementations
        }

        // New usage
        var logger = new ConsoleLogger();
        logger.LogError("This is an error message."); // Uses default implementation from ILogger without needing changes in ConsoleLogger
  ```

### Pattern Matching Enhancements

- **Switch Expressions**: Simplify how you use switch statements by writing less code and making it more readable. It lets you switch based on the value of a variable more concisely.

- **Property Patterns**: Check an object's properties directly within a pattern match. It allows for more detailed examination of an object’s structure in a cleaner way.

- **Tuple Patterns**: Useful for checking the elements of a tuple (a data structure that can hold multiple items) directly in a pattern match. This makes it easier to work with grouped data.

- **Positional Patterns**: Enable you to check values in an object based on their position, rather than needing to know the name of each property. This is great for working with data where the order matters but not the specific names of the properties.

- **Recursive Patterns**: Allow patterns to be nested inside one another, providing a way to perform complex checks in a very structured manner. This is especially powerful for working with hierarchical or nested data.

  ```csharp
    public abstract class Shape {}

    public class Circle : Shape
    {
        public double Radius { get; }
        public Circle(double radius) => Radius = radius;
    }

    public class Rectangle : Shape
    {
        public double Length { get; }
        public double Width { get; }
        public Rectangle(double length, double width) => (Length, Width) = (length, width);
    }

    public class Triangle : Shape
    {
        public double Base { get; }
        public double Height { get; }
        public Triangle(double @base, double height) => (Base, Height) = (@base, height);
    }

    // Composite shape to demonstrate recursive patterns
    public class CompositeShape : Shape
    {
        public Shape InnerShape1 { get; }
        public Shape InnerShape2 { get; }
        public CompositeShape(Shape shape1, Shape shape2) => (InnerShape1, InnerShape2) = (shape1, shape2);
    }

    // conventional switch statement
    static string DescribeShape(Shape shape)
    {
        switch (shape)
        {
            case Circle c:
                return "This is a circle.";
            case Rectangle r:
                return $"This is a rectangle with length {r.Length} and width {r.Width}.";
            case Triangle t:
                return $"This is a triangle with base {t.Base} and height {t.Height}.";
            case CompositeShape cs when cs.InnerShape1 is Circle && cs.InnerShape2 is Rectangle:
                return "This is a composite shape containing a circle and a rectangle.";
            default:
                return "This is an unknown shape.";
        }
    }

    // New switch expression
    static string DescribeShape(Shape shape) => shape switch
    {
        // Type Pattern
        Circle => "This is a circle.",

        // Property Pattern
        Rectangle { Length: var l, Width: var w } => $"This is a rectangle with length {l} and width {w}.",

        // Positional Pattern for Tuple
        Triangle (var baseLength, var height) => $"This is a triangle with base {baseLength} and height {height}.",

        // Recursive Pattern with Property and Type Patterns
        CompositeShape(var innerShape1, var innerShape2) when innerShape1 is Circle && innerShape2 is Rectangle => "This is a composite shape containing a circle and a rectangle.",

        // Catch-all for other shapes or unmatched composite shapes
        _ => "This is an unknown shape."
    };
  ```

### Using Declarations

- C# 8.0 introduced an enhancement to the using statement, known as "Using Declarations." This enhancement simplifies resource management in your code. Previously, when using the using statement to ensure that resources are properly disposed of, your code needed explicit braces to define the scope where the resources would be used, making the code more verbose.

With C# 8.0, "Using Declarations" allow you to declare resources that should be disposed of at the end of the current scope without needing explicit braces, making the syntax cleaner and reducing the boilerplate code associated with resource management. This feature automatically disposes of the object when it goes out of scope, enhancing code readability and efficiency. However, it's important to use this feature carefully to avoid disposing of objects prematurely, especially in complex scopes.

- **Using Declarations Syntax**

  ```csharp
      using System;
      using System.IO;

      class Program
      {
          static void Main()
          {
              // Example file path.
              string filePath = "example.txt";

              // Writing to a file with using declaration.
              // The StreamWriter is automatically disposed at the end of the current scope.
              using var writer = new StreamWriter(filePath);
              writer.WriteLine("Hello, World!");
              writer.WriteLine("Using Declarations in C# 8.0 are awesome!");

            Console.WriteLine("Content written to file successfully.");

            // Reading from a file with using declaration.
            // The StreamReader is automatically disposed at the end of the current scope.
            Console.WriteLine("\nReading from the file:");
            using var reader = new StreamReader(filePath);
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }
    }
  ```

### Static Local Functions

- The "Static Local Functions" enhancement in C# 8.0 introduces the ability to declare local functions as static. This feature allows these functions to be explicitly marked to prevent them from capturing any variables from the enclosing method's scope. As a result, a static local function cannot access or modify any variables defined in its surrounding method, leading to safer and more predictable code, especially in terms of avoiding unintended captures of the method's context. This enhancement encourages better practices in managing local functions, making the code more efficient by reducing overhead and potential errors related to variable capture

```csharp
    class StaticLocalFunctionExample
    {
        public void CalculateAndPrintSum(int[] numbers)
        {
            int localAdd(int x, int y) => x + y;
            static int localStaticAdd(int x, int y) => x + y;

            int sum = 0;
            foreach (var number in numbers)
            {
                // Example of calling a regular local function
                sum = localAdd(sum, number);

                // Example of calling a static local function
                // Note: Static local functions can't access variables defined
                // outside their scope. The following line would generate
                // a compilation error if 'sum' and 'number' were used directly.
                sum = localStaticAdd(sum, number);
            }
            Console.WriteLine($"The sum is: {sum}");
        }
    }
```

### Disposable Ref Structs

- In C# 8.0, a significant feature called "Disposable Ref Structs" was introduced. This feature enhances the management of memory and resource handling, especially in high-performance applications. Disposable ref structs allow structures (structs) that use the ref keyword to implement the IDisposable interface. This means that these structs can now be used with a using statement or expression, ensuring that resources are automatically released when they are no longer needed.

- Previously, structs could not directly implement interfaces, including IDisposable. However, with C# 8.0, this limitation has been addressed for ref structs. This is particularly useful for managing unmanaged resources, like file handles or database connections, within a struct that requires manual cleanup.

- This feature is part of several enhancements aimed at improving the performance and safety of resource management in C#. It helps prevent resource leaks by making it clearer and easier to implement deterministic resource cleanup on types that are typically used for low-level, high-performance scenarios.

- In essence, disposable ref structs combine the efficiency and low overhead of structs with improved resource management capabilities, making C# more effective for systems-level programming and high-performance applications.

```csharp
    // Define a disposable ref struct that represents an unmanaged memory buffer
    public ref struct UnmanagedBuffer
    {
        private IntPtr bufferPointer; // Pointer to the unmanaged buffer

        public UnmanagedBuffer(int size)
        {
            // Allocate memory for the buffer using some unmanaged API
            bufferPointer = Marshal.AllocHGlobal(size);
        }

        // Implement the Dispose method to release the unmanaged resource
        public void Dispose()
        {
            if (bufferPointer != IntPtr.Zero)
            {
                Marshal.FreeHGlobal(bufferPointer);
                bufferPointer = IntPtr.Zero;
            }
        }
    }

    // Usage example demonstrating automatic resource management with 'using'
    public class BufferProcessor
    {
        public void ProcessBuffer(int size)
        {
            // The UnmanagedBuffer will be automatically disposed at the end of the using block
            using (var buffer = new UnmanagedBuffer(size))
            {
                // Perform operations using the unmanaged buffer...
            }
        }
    }
```

### Nullable Reference Types

- In C# 8.0, the feature of "Nullable Reference Types" introduces a significant enhancement aimed at reducing the occurrence of null reference exceptions, a common source of bugs in software development. This feature allows developers to explicitly declare when a reference type is expected to allow null values by adding a question mark (?) to the type declaration. When enabled, the compiler provides warnings when code might dereference null, helping developers catch potential null reference exceptions during the compilation phase rather than at runtime.

- For example, a reference type variable declared without a question mark is considered non-nullable, and attempting to assign null to it will generate a compiler warning. Conversely, declaring a variable with a question mark indicates that it can hold a null value, prompting the developer to check for null before accessing its members or methods. This feature is opt-in at the project level, allowing developers to gradually adopt it and improve the null safety of their codebase.

- By enabling nullable reference types, developers can write more robust and reliable code by catching null reference exceptions early in the development process. This feature encourages better null handling practices and helps prevent common sources of bugs related to null values.

```csharp
    #nullable enable

    public class NullableReferenceTypesExample
    {
        public string? GetFullName(string firstName, string lastName)
        {
            // Nullable reference types enabled, so 'null' is allowed for string types
            if (string.IsNullOrEmpty(firstName) || string.IsNullOrEmpty(lastName))
            {
                return null; // Returning 'null' is allowed for nullable reference types
            }

            return $"{firstName} {lastName}";
        }

        public void PrintFullName(string? fullName)
        {
            // Nullable reference types enabled, so 'null' is allowed for string types
            if (fullName != null)
            {
                Console.WriteLine(fullName.Length); // Warning: Possible dereference of a null reference
            }
        }
    }

    #nullable enable

    using System;

    namespace UserProfileManagement
    {
        public class UserProfile
        {
            // Non-nullable reference type: must always have a value.
            public string Name { get; set; }

            // Nullable reference types: may or may not have a value.
            public string? Biography { get; set; }
            public string? TwitterHandle { get; set; }

            public UserProfile(string name)
            {
                Name = name ?? throw new ArgumentNullException(nameof(name), "User name cannot be null.");
                Biography = null;
                TwitterHandle = null;
            }

            public void DisplayProfile()
            {
                Console.WriteLine($"Name: {Name}");
                Console.WriteLine($"Biography: {Biography ?? "N/A"}");
                Console.WriteLine($"Twitter: @{TwitterHandle ?? "N/A"}");
            }
        }

        class Program
        {
            static void Main(string[] args)
            {
                var user = new UserProfile("John Doe")
                {
                    Biography = "Developer, writer, and speaker.",
                    TwitterHandle = "johndoe"
                };

                user.DisplayProfile();
            }
        }
    }
```

### Asynchronous Streams

- In C# 8.0, the enhancement introduced as "Asynchronous Streams" allows for the iteration over collections that are loaded asynchronously, providing a more streamlined way to handle streaming data or sequences that arrive over time. This feature is particularly useful for applications that interact with data sources where elements are fetched or produced asynchronously, such as web services, files, or real-time data feeds. Asynchronous streams utilize the `IAsyncEnumerable<T>` interface to represent asynchronous sequences, and they can be consumed with an `await foreach` loop, allowing each element to be awaited as it becomes available. This integration of async and await into the iteration process simplifies the code needed to work with asynchronous data streams, making it more readable and maintainable

```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    class Program
    {
        static async Task Main(string[] args)
        {
            // Example: Consuming an asynchronous stream
            // We call the asynchronous method GetSensorDataAsync and await each value as it's available
            await foreach (var sensorData in GetSensorDataAsync())
            {
                Console.WriteLine($"Received sensor data: {sensorData}");
            }
        }

        // This method simulates an asynchronous stream of data from a sensor
        // It uses "async" keyword with "IAsyncEnumerable<T>" to signify an asynchronous stream
        static async IAsyncEnumerable<int> GetSensorDataAsync()
        {
            var random = new Random();
            // Simulating streaming of data with a loop
            for (int i = 0; i < 10; i++)
            {
                // Simulate data delay
                await Task.Delay(1000); // Wait 1 second
                // Yield return to produce a value for the asynchronous stream
                yield return random.Next(0, 100); // Returns a random sensor value
            }
        }
    }
```

- `IAsyncEnumerable<int>`: This interface signifies an asynchronous stream that can produce elements over time. Each element in the stream can be awaited as it becomes available.
- `await foreach`: This syntax is used to consume an asynchronous stream. The loop iterates over each element as it's produced by the asynchronous stream, awaiting each one before moving to the next.
- `GetSensorDataAsync` method: Simulates an asynchronous stream of sensor data. This could represent data coming from a device, a file, or a web service. The method uses `yield return` within a loop to produce elements asynchronously.
- `await Task.Delay(1000);`: Inside the loop, this line simulates a delay to mimic the asynchronous nature of data coming in over time. This could represent waiting for new data to be available from a sensor.
- `yield return random.Next(0, 100);`: This line produces a value for the stream. In a real-world scenario, this would be replaced with actual data retrieval logic.

### Indices and Ranges

- In C# 8.0, the new feature related to "Indices and Ranges" enhances the way developers can work with arrays and collections. This enhancement introduces two new types: System.Index and System.Range, which simplify accessing elements and slices of arrays. An Index can represent a position from the start or end of a sequence, making it easier to reference elements without calculating their position explicitly. The syntax uses a caret ^ for counting from the end. For example, ^1 represents the last element, ^2 the second to last, and so on.

- Ranges, represented by the .. operator, allow developers to specify a start and an end for slicing collections, where the start index is inclusive and the end index is exclusive. This feature provides a more readable and concise syntax for accessing sub-sections of arrays or collections, enhancing code clarity and maintainability. For instance, array[1..3] retrieves elements at positions 1 and 2, excluding 3.

```csharp
    using System;

    class IndicesAndRangesDemo
    {
        static void Main()
        {
            // Define an array of strings representing days of the week
            string[] daysOfWeek = new string[]
            {
                "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"
            };

            // Using an Index to access the last element with '^'
            // '^1' represents the last element in the array
            Console.WriteLine($"Last day of the week: {daysOfWeek[^1]}"); // Output: "Sunday"

            // Using a Range to get the middle days of the week (Tuesday to Thursday)
            // The range '1..4' starts from the second element (index 1) up to, but not including, the fifth element (index 4)
            string[] middleDays = daysOfWeek[1..4];
            Console.WriteLine("Middle days of the week: ");
            foreach (var day in middleDays)
            {
                Console.WriteLine(day); // Output: Tuesday, Wednesday, Thursday
            }

            // Using an open-ended range to get all days from Thursday to the end
            // The range '3..' starts from the fourth element (index 3) to the end of the array
            var daysFromThursday = daysOfWeek[3..];
            Console.WriteLine("Days from Thursday to the end of the week: ");
            foreach (var day in daysFromThursday)
            {
                Console.WriteLine(day); // Output: Thursday, Friday, Saturday, Sunday
            }

            // Using an Index and Range together to access a specific element
            // Accessing the second to last element using the '^' operator inside a range
            var secondToLastDay = daysOfWeek[^2..^1][0];
            Console.WriteLine($"Second to last day of the week: {secondToLastDay}"); // Output: "Saturday"

            // Using ranges to create a slice with the first three days of the week
            // The range '..3' means from the start of the array up to, but not including, the fourth element (index 3)
            var firstThreeDays = daysOfWeek[..3];
            Console.WriteLine("First three days of the week: ");
            foreach (var day in firstThreeDays)
            {
                Console.WriteLine(day); // Output: Monday, Tuesday, Wednesday
            }
        }
    }
```

### Null-Coalescing Assignment

- In C# 8.0, the "Null-Coalescing Assignment" feature is a handy enhancement that simplifies the way you can assign values to variables that might be null. In simpler terms, it allows you to assign a value to a variable only if that variable is currently null. This makes your code cleaner and more readable by reducing the need for boilerplate null-checking code.

- Here's how it works: Imagine you have a variable that might or might not be null, and you want to ensure it has a value. Instead of writing a full if statement to check if the variable is null and then assigning a value, you can use the null-coalescing assignment operator ??=. This operator checks if the variable on its left-hand side is null; if it is, it assigns the value from the right-hand side to the variable.

- This feature is particularly useful for initializing objects or setting default values efficiently without adding extra if checks throughout your code.

  ```csharp
    using System;
    using System.Collections.Generic;

    class Program
    {
        static void Main(string[] args)
        {
            // Example 1: Initializing a possibly null object
            List<string> names = null;

            // Instead of using an if statement to check if 'names' is null and then initializing it
            // like this:
            // if (names == null)
            // {
            //     names = new List<string>();
            // }
            // You can simply use the null-coalescing assignment operator.
            names ??= new List<string>(); // 'names' is null, so it gets initialized here

            names.Add("John");
            names.Add("Jane");
            Console.WriteLine($"Example 1: Names count is {names.Count}.");

            // Example 2: Setting default value for nullable types
            int? someNumber = null;

            // Similarly, for nullable value types, instead of writing:
            // if (someNumber == null)
            // {
            //     someNumber = 10;
            // }
            // You can use:
            someNumber ??= 10; // 'someNumber' is null, so it gets assigned the value 10 here

            Console.WriteLine($"Example 2: The number is {someNumber}.");

            // Example 3: Working with reference types
            string customMessage = null;

            // This is useful when you need to ensure a reference type has a value
            // but want to avoid overwriting it if it's already set.
            // Instead of:
            // if (customMessage == null)
            // {
            //     customMessage = "Default Message";
            // }
            // Use the null-coalescing assignment:
            customMessage ??= "Default Message"; // 'customMessage' is null, so it gets assigned

            Console.WriteLine($"Example 3: The message is: {customMessage}");
        }
    }
  ```

- **Initializing a possibly null object**: If the `names` list is null, it gets initialized to a new list. This is particularly useful for lazy initialization of objects.

- **Setting default values for nullable types**: The `someNumber` variable, a nullable int, is given a default value if it's found to be null. This is a concise way to ensure variables have sensible defaults.

- **Working with reference types**: The `customMessage` string is assigned a default value only if it is currently null. This technique avoids overwriting existing values while ensuring the variable is not null.

### Unmanaged Constructed Types

- In C# 8.0, the concept of "Unmanaged constructed types" enhances performance and memory management, particularly useful for high-performance applications. Previously, a constructed type (a type with one or more type arguments) could not be considered unmanaged. C# 8.0 changes this by allowing constructed value types to be considered unmanaged if all of their fields are of unmanaged types. This update means you can now create pointers to variables of these types and allocate them on the stack, which was not possible before C# 8.0. This feature is crucial for developers looking to optimize memory usage and improve the execution speed of their applications by utilizing stack allocation for more complex types.

  ```csharp
      using System;

      public struct Pair<T> where T : unmanaged
      {
          public T First;
          public T Second;

          // Constructor to initialize the pair
          public Pair(T first, T second)
          {
              First = first;
              Second = second;
          }

          // Method to display the values
          public void Display()
          {
              Console.WriteLine($"First: {First}, Second: {Second}");
          }
      }

      class Program
      {
          static void Main(string[] args)
          {
              // Example usage with integers
              Pair<int> intPair = new Pair<int>(1, 2);

              // Using pointers to manipulate Pair<int>
              unsafe
              {
                  Pair<int>* ptr = &intPair;
                  ptr->Display(); // Displaying values using pointer
              }

              // Allocating array of Pair<float> on the stack
              unsafe
              {
                  // Allocating 5 pairs on the stack
                  Span<Pair<float>> pairSpan = stackalloc Pair<float>[5];
                  for (int i = 0; i < pairSpan.Length; i++)
                  {
                      pairSpan[i] = new Pair<float>(i * 1.1f, i * 2.2f);
                  }

                  // Displaying the values of each pair
                  foreach (var pair in pairSpan)
                  {
                      pair.Display();
                  }
              }
          }
      }
  ```

- We define a generic `Pair<T>` struct with the constraint `where T : unmanaged`, ensuring that `T` can only be an unmanaged type. This allows the `Pair<T>` struct itself to be unmanaged when `T` is unmanaged.
- In the `Main` method, we demonstrate creating an instance of `Pair<int>`. Since `int` is an unmanaged type, `Pair<int>` is also unmanaged, allowing us to use pointers with it.
- We use `unsafe` blocks to work with pointers. This is necessary because working directly with memory addresses is considered unsafe in C#.
- We showcase allocating an array of `Pair<float>` on the stack using the `stackalloc` keyword. `Span<T>` is used to safely work with the memory on the stack.
- The example includes a method (`Display`) within the `Pair<T>` struct to output its values, demonstrating how instances of the struct can be used with both pointer syntax and within a stack-allocated span.

### Stackalloc in Nested Expressions

- In C# 8.0, a notable enhancement involves the `stackalloc` keyword in nested expressions. This change allows for more flexible memory allocation within stack frames, making it easier for developers to manage memory for arrays and spans efficiently in performance-critical applications. Before this update, `stackalloc` was restricted to simpler contexts, but with C# 8.0, it can be used in a broader range of expressions, offering greater control over memory allocation directly on the stack. This means developers can now initialize arrays or spans inline within more complex expressions, optimizing performance by avoiding heap allocations

  ```csharp
      using System;

      class StackAllocNestedExample
      {
          static void Main(string[] args)
          {
              // Example of stackalloc in a nested expression with C# 8.0
              // We're using stackalloc within a call to a method that calculates the sum of a span of integers.

              int sum = SumSpan(stackalloc int[] { 1, 2, 3, 4, 5 });
              Console.WriteLine($"The sum is: {sum}");

              // Demonstrating the use of stackalloc with Span<T> in a more complex scenario
              // Here, we create a span of Fibonacci numbers on the stack and then calculate their sum.

              Span<int> fibonacciSpan = stackalloc int[5];
              fibonacciSpan[0] = 1; // Initializing the first Fibonacci number
              fibonacciSpan[1] = 1; // Initializing the second Fibonacci number

              // Generating the next Fibonacci numbers
              for (int i = 2; i < fibonacciSpan.Length; i++)
              {
                  fibonacciSpan[i] = fibonacciSpan[i - 1] + fibonacciSpan[i - 2];
              }

              int fibonacciSum = SumSpan(fibonacciSpan);
              Console.WriteLine($"The sum of the first 5 Fibonacci numbers is: {fibonacciSum}");
          }

          // Method to calculate the sum of a span of integers
          static int SumSpan(Span<int> numbers)
          {
              int sum = 0;
              foreach (int number in numbers)
              {
                  sum += number;
              }
              return sum;
          }
      }
  ```

- In this example, we first demonstrate a straightforward use of `stackalloc` in a nested expression where a span of integers is allocated on the stack and immediately passed to the `SumSpan` method, which calculates their sum.
- The `SumSpan` method is a simple function that takes a `Span<int>` as an argument and iterates over its elements to calculate the sum. This demonstrates how methods can work with spans, including those allocated on the stack using `stackalloc`.
- Next, we show a more complex scenario involving generating and summing the first five Fibonacci numbers. We allocate a span for five integers on the stack, manually initialize the first two Fibonacci numbers, and then use a loop to calculate the rest. After generating the Fibonacci numbers, we pass the span to the same `SumSpan` method to calculate their sum.
- This example highlights the enhanced capability of C# 8.0 to use `stackalloc` in nested expressions and work with spans in a performance-efficient manner, avoiding heap allocations.

### Enhancement of Interpolated Verbatim Strings

- In C# 8.0, a small but neat enhancement was made to interpolated verbatim strings. Previously, to create such a string, you had to use the syntax `$@"text"`, placing the dollar sign before the at symbol. C# 8.0 allows you to reverse the order, writing `@$"text"` instead. This change offers an alternative way to write interpolated verbatim strings, making it more flexible for developers to choose the order of symbols that feels most intuitive to them.

```csharp
    using System;

    class Program
    {
        static void Main()
        {
            // Define some variables to use in our interpolated verbatim strings
            string name = "John Doe";
            int age = 30;
            string occupation = "Software Developer";

            // Using C# 7 syntax for interpolated verbatim string
            // Notice the $ comes before the @
            // This is useful for including variables in a string that spans multiple lines or contains escape sequences
            string bioCSharp7 = $@"Name: {name}
            Age: {age}
            Occupation: {occupation}
            New Lines and \tTabs are preserved as is.";

            Console.WriteLine("C# 7 Syntax:");
            Console.WriteLine(bioCSharp7);

            // Using C# 8.0 enhancement for interpolated verbatim string
            // Notice we can now start with @ followed by $
            // This does not change the functionality but provides a syntax preference
            string bioCSharp8 = @$"Name: {name}
            Age: {age}
            Occupation: {occupation}
            New Lines and \tTabs are preserved as is.";

            Console.WriteLine("\nC# 8.0 Syntax:");
            Console.WriteLine(bioCSharp8);

            // Example to show path manipulation using the new syntax
            // Very handy for file paths on Windows where backslashes are used
            string directory = "C:\\Users\\JohnDoe";
            string fileName = "bio.txt";

            // Combining paths with variables and avoiding confusion with escape sequences
            string filePath = @$"{directory}\{fileName}";

            Console.WriteLine("\nFile Path Example with C# 8.0 Syntax:");
            Console.WriteLine(filePath);

            // The choice between C# 7 and C# 8 syntax for interpolated verbatim strings can depend on personal or team preference
            // The functionality remains the same, providing a versatile tool for string manipulation in C#
        }
    }
```

### Target-Typed New Expressions

- In C# 8.0, a notable feature called "Target-Typed New Expressions" was introduced to streamline and improve code readability. This enhancement allows developers to instantiate objects without explicitly specifying the type of the object when the type is already known from the context. This feature reduces redundancy by allowing the use of `new` with the assignment operation directly, assuming the left-hand side of the assignment offers enough information to deduce the type of the object being created. This leads to cleaner and more concise code, especially in situations where the type name is verbose or when initializing properties or arrays with obvious types. This enhancement contributes to making code more straightforward and readable by focusing on the essential information and reducing boilerplate code.

  ```csharp
      // Assume we have a class Car defined as follows:
      public class Car
      {
          public string Make { get; set; }
          public string Model { get; set; }
          public int Year { get; set; }
      }

      // Before C# 8.0, when initializing a new object, you needed to specify the type explicitly:
      Car myOldCar = new Car { Make = "Toyota", Model = "Camry", Year = 2010 };

      // With C# 8.0, using Target-Typed New Expressions, the type can be inferred from the variable's declared type.
      // This is particularly useful when the type name is long or when it's clear from the context.
      Car myNewCar = new() { Make = "Honda", Model = "Civic", Year = 2020 };

      // This feature becomes even more beneficial in more complex scenarios, such as collections or when dealing with generics.
      // For instance, consider initializing a list of cars:
      List<Car> carList = new()
      {
          new() { Make = "Ford", Model = "Mustang", Year = 2021 },
          new() { Make = "Chevrolet", Model = "Corvette", Year = 2021 }
      };

      // Or when working with dictionaries:
      Dictionary<string, Car> carDictionary = new()
      {
          ["car1"] = new() { Make = "Nissan", Model = "Sentra", Year = 2018 },
          ["car2"] = new() { Make = "Hyundai", Model = "Elantra", Year = 2019 }
      };

      // The key advantage here is that the code is more concise without losing clarity. It's particularly useful in reducing
      // the verbosity when the type is already apparent from the context, making the code more readable and maintainable.
  ```

## Conclusion

The introduction of C# 8.0 marks a significant advancement in the language, providing developers with powerful tools to write safer, more expressive, and efficient code. With features like nullable reference types for preventing null reference exceptions, asynchronous streams for better handling of data flows, and enhancements in pattern matching for more concise code, C# 8.0 is poised to enhance software development practices. These improvements not only cater to the evolving needs of modern applications but also emphasize code clarity and performance, making C# an even more robust and versatile programming language for a wide range of projects.
