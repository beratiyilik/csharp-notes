# C# 12.0 New Features

## Table of Contents

- [C# 12.0 New Features](#c-120-new-features)
  - [Table of Contents](#table-of-contents)
  - [Introduction and Overview](#introduction-and-overview)
  - [New Features](#new-features)
    - [Primary Constructors](#primary-constructors)
    - [Inline Arrays](#inline-arrays)
    - [Default Lambda Parameters](#default-lambda-parameters)
    - [ref readonly Parameters](#ref-readonly-parameters)
    - [Alias Any Type with using](#alias-any-type-with-using)
    - [Experimental Attribute](#experimental-attribute)
    - [Interceptors](#interceptors)
    - [Collection Expressions](#collection-expressions)
  - [Conclusion](#conclusion)

## Introduction and Overview

C# 12.0 introduces a series of enhancements aimed at improving code clarity, reducing boilerplate, and enhancing performance, making it a significant update for developers working with the .NET framework. This version includes notable features such as Primary Constructors, which simplify the declaration of constructors in classes and structs, Inline Arrays that optimize performance by allowing fixed-size arrays within structs, and Default Lambda Parameters which enhance the flexibility of lambda expressions. Additional features like `ref readonly` parameters, the use of `using` to alias any type, and the introduction of Collection Expressions streamline various aspects of coding in C#. Experimental Attributes and Interceptors are also introduced to help developers experiment with new ideas safely and efficiently. These improvements collectively enhance the language’s expressiveness and safety, aiming to make C# a more robust choice for both high-level and low-level programming tasks.

## New Features

### Primary Constructors

The new enhancement in C# 12.0 concerning "Primary Constructors" allows classes and structs to define constructors directly in their declarations, rather than needing separate constructor definitions. This feature was previously limited to record types. Primary constructors let you declare and initialize parameters at the point of type declaration, simplifying code by reducing the need for additional boilerplate constructor code. This enhancement is significant because it extends the flexibility and simplicity that was exclusive to records, to all class and struct types, streamlining object initialization and maintenance in C#.

- **Basic Usage in a Class**

```csharp
// Define a class with a primary constructor.
// The primary constructor allows direct parameter definition in the class declaration.
public class Employee(string name, int age)
{
    // Properties are initialized directly from constructor parameters.
    public string Name { get; } = name;
    public int Age { get; } = age;

    // Additional member methods can utilize constructor parameters.
    public void DisplayInfo()
    {
        Console.WriteLine($"Name: {Name}, Age: {Age}");
    }
}

// Usage
var emp = new Employee("John Doe", 30);
emp.DisplayInfo();  // Output: Name: John Doe, Age: 30
```

- **Using Primary Constructors with Inheritance**

```csharp
// Base class with a primary constructor
public class Person(string name)
{
    public string Name { get; } = name;
}

// Derived class that also uses a primary constructor and calls the base class constructor.
public class Student(string name, string university) : Person(name)
{
    public string University { get; } = university;

    public void DisplayInfo()
    {
        Console.WriteLine($"Name: {Name}, University: {University}");
    }
}

// Usage
var student = new Student("Alice Johnson", "MIT");
student.DisplayInfo();  // Output: Name: Alice Johnson, University: MIT
```

- **Structs with Primary Constructors**

```csharp
// Defining a struct with a primary constructor.
public struct Point(int x, int y)
{
    public int X { get; } = x;
    public int Y { get; } = y;

    // Method to display the point coordinates
    public void DisplayCoordinates()
    {
        Console.WriteLine($"Point coordinates: ({X}, {Y})");
    }
}

// Usage
var p = new Point(10, 20);
p.DisplayCoordinates();  // Output: Point coordinates: (10, 20)
```

### Inline Arrays

The new "Inline Arrays" feature in C# 12.0 allows developers to create arrays of a fixed size within a struct type, enhancing performance by mimicking the behavior of unsafe fixed-size buffers but within safe code. This feature is mainly used by library authors to optimize applications, offering performance characteristics similar to those of an unsafe array without requiring unsafe code. Inline arrays are declared within a struct, and you can access elements similarly to traditional arrays. This enhancement is part of C#'s broader efforts to provide more powerful, low-level programming capabilities in a safe manner.

```csharp
using System;

// Define a struct with an Inline Array attribute specifying the size of the inline array
[System.Runtime.CompilerServices.InlineArray(10)]
public struct FixedSizeBuffer
{
    // The private field _element0 is necessary but unused directly.
    // It signifies the start of the inline array in memory.
    private int _element0;

    // Indexer to provide access to the inline array elements in a safe manner
    public int this[int index]
    {
        get
        {
            // Safely access the inline array elements using the System.Runtime.CompilerServices.Unsafe class
            return System.Runtime.CompilerServices.Unsafe.Add(ref _element0, index);
        }
        set
        {
            // Safely modify the inline array elements
            System.Runtime.CompilerServices.Unsafe.Add(ref _element0, index) = value;
        }
    }
}

class Program
{
    static void Main(string[] args)
    {
        // Create an instance of the struct
        FixedSizeBuffer buffer = new FixedSizeBuffer();

        // Populate the inline array
        for (int i = 0; i < 10; i++)
        {
            buffer[i] = i * i; // Store square of the index
        }

        // Read and print elements from the inline array
        for (int i = 0; i < 10; i++)
        {
            Console.WriteLine(buffer[i]); // Output the square of the index
        }
    }
}
```

- **Inline Array Declaration**: The `FixedSizeBuffer` struct is marked with `[System.Runtime.CompilerServices.InlineArray(10)]`, indicating it has an inline array of 10 integers.
- **Private Field `_element0`**: This field represents the starting point of the inline array in memory. It is necessary for the low-level memory operations used to access the array elements but isn't used directly in typical C# operations.
- **Indexer Implementation**: The indexer provides a way to access and modify the elements of the inline array safely. It uses the `Unsafe.Add` method to calculate the memory offset from `_element0` and access the desired element. This approach ensures type safety and bounds checking typical of C# arrays while operating at a lower level close to the hardware.
- **Usage**: In the `Main` method, an instance of `FixedSizeBuffer` is created and populated using a loop that squares the index. Another loop reads these values back and prints them, demonstrating how the inline array can be used like any other array in C#.

### Default Lambda Parameters

In C# 12.0, a notable new feature is "Default Lambda Parameters." This enhancement allows developers to specify default values for parameters in lambda expressions. The syntax and rules for defining these default values are consistent with those used for method parameters. This feature simplifies the use of lambda expressions by enabling callers to omit arguments for which defaults have been specified, making the code more concise and flexible.

```csharp
// Simple Default Lambda Parameter
// This lambda expression adds a specified increment to a source number.
// If no increment is specified, it defaults to 1.
var increment = (int number, int step = 1) => number + step;

// Usage of the lambda with default parameter
Console.WriteLine(increment(5));  // Output: 6, because default step is 1
Console.WriteLine(increment(5, 2)); // Output: 7, because step is explicitly set to 2

// Lambda with Multiple Default Parameters
// This lambda expression calculates the area of a rectangle.
// It assumes default values for length and width if they are not specified.
var calculateArea = (int length = 10, int width = 5) => length * width;

// Usage of the lambda with all defaults
Console.WriteLine(calculateArea()); // Output: 50 (default values used: length=10, width=5)
// Usage of the lambda with one default
Console.WriteLine(calculateArea(7)); // Output: 35 (specified length=7, default width=5)
// Usage of the lambda without defaults
Console.WriteLine(calculateArea(7, 2)); // Output: 14 (specified length=7, width=2)

// Using Default Lambda Parameters with Action Delegates
// This lambda logs a message with a default severity level.
Action<string, string> log = (message, severity = "info") =>
{
    Console.WriteLine($"[{severity.ToUpper()}]: {message}");
};

// Usage of the lambda with default parameter
log("User logged in."); // Output: [INFO]: User logged in.
// Usage of the lambda without using the default parameter
log("User not found", "error"); // Output: [ERROR]: User not found

// Combining Lambda Expressions with Default and Optional Parameters
// This lambda expression filters a list of numbers, excluding those below a specified threshold.
// It allows specifying an optional upper limit.
Func<List<int>, int, int?, List<int>> filterNumbers = (numbers, lowerLimit, upperLimit = null) =>
{
    return numbers.Where(n => n >= lowerLimit && (upperLimit == null || n <= upperLimit)).ToList();
};

// Sample data
List<int> numbers = new List<int> { 1, 5, 10, 15, 20, 25 };

// Usage of the lambda with an optional upper limit
var filtered = filterNumbers(numbers, 10, 20);
Console.WriteLine(string.Join(", ", filtered)); // Output: 10, 15, 20
// Usage of the lambda without the optional upper limit
filtered = filterNumbers(numbers, 10);
Console.WriteLine(string.Join(", ", filtered)); // Output: 10, 15, 20, 25
```

### ref readonly Parameters

The new feature of "ref readonly parameters" in C# 12.0 provides a way to pass arguments to methods by reference, ensuring they cannot be modified. This feature enhances code clarity and safety by allowing the developer to specify that the passed reference should only be read, not altered. It offers an alternative to the "in" parameter modifier, which already allows passing by reference without modification, but "ref readonly" makes the intent more explicit when the parameter must be a variable. This can be particularly useful in scenarios where APIs previously used "ref" even though the data was not modified. It ensures better performance compared to passing by value, especially with large data structures, without the risk of accidental data modification.

- **Using `ref readonly` with a method**

```csharp
public struct Vector3
{
    public double X, Y, Z;

    // This method accepts a ref readonly parameter, ensuring the vector cannot be modified.
    public static double ComputeMagnitude(ref readonly Vector3 vector)
    {
        // Calculates the magnitude based on the vector components.
        return Math.Sqrt(vector.X * vector.X + vector.Y * vector.Y + vector.Z * vector.Z);
    }
}

public class Program
{
    public static void Main()
    {
        var v = new Vector3 { X = 3, Y = 4, Z = 5 };
        double magnitude = Vector3.ComputeMagnitude(ref v);
        Console.WriteLine($"Magnitude: {magnitude}");
    }
}
```

- This example defines a `Vector3` struct and computes the magnitude of the vector using a method that takes a `ref readonly` parameter. This ensures that the vector passed to the method cannot be modified, which helps in maintaining data integrity and improves performance by avoiding data copying.

- **`ref readonly` in overloading scenarios**

```csharp
public class RefReadonlyDemo
{
    // Overload that takes a ref readonly parameter
    public void Display(ref readonly int value)
    {
        Console.WriteLine($"Readonly value: {value}");
    }

    // Overload that takes a ref parameter
    public void Display(ref int value)
    {
        value += 10; // This method can modify the passed parameter
        Console.WriteLine($"Modified value: {value}");
    }
}

public class Program
{
    public static void Main()
    {
        int original = 5;
        var demo = new RefReadonlyDemo();

        // Using ref readonly overload
        demo.Display(ref original);

        // Using ref overload
        demo.Display(ref original);
        Console.WriteLine($"Original value after modification: {original}");
    }
}
```

- In this example, method overloading is used with `ref` and `ref readonly` to show how the same function can be safely executed with or without allowing modifications to the passed parameters. This approach provides flexibility in how methods interact with data depending on the need for data modification.

### Alias Any Type with using

In C# 12.0, The enhancement of "Alias Any Type with using" broadens the scope of alias directives to include any type of data structure, not just named types. Previously, C# allowed aliases for namespaces and specific named types such as classes and interfaces. With this new feature, developers can now create aliases for a wider array of types, including tuples, arrays, and pointer types. This enhancement simplifies the code by allowing developers to use shorter, more meaningful names for complex or frequently used types, improving code readability and maintainability

- **Aliasing a Tuple Type**

```csharp
// Allows using a simple name for a commonly used tuple structure in the code.
using Point = (int x, int y);

public void Translate(Point p)
{
    Console.WriteLine($"Translating to coordinates ({p.x}, {p.y})");
}
```

- **Aliasing Array Types**

```csharp
// Simplifies referencing complex array types through an alias.
using IntMatrix = int[,];

public void ProcessMatrix(IntMatrix matrix)
{
    // Processing code here
    Console.WriteLine($"Processing a matrix of size {matrix.GetLength(0)}x{matrix.GetLength(1)}");
}
```

- **Aliasing Pointer Types (requires unsafe context)**

```csharp
// Useful for code that involves interoperability or low-level memory manipulation.
using IntPtr = int*;

public unsafe void Increment(IntPtr ptr)
{
    (*ptr)++;
}
```

### Experimental Attribute

The new "Experimental Attribute" in C# 12.0 allows developers to mark code elements as experimental, indicating that they are not yet stable or finalized. This attribute is useful for library authors and framework developers who want to expose new features or APIs for testing and feedback without committing to a stable interface. By marking code as experimental, developers can communicate the status of the feature to users and other developers, encouraging them to provide feedback and report issues. This feature helps maintain code quality and stability by clearly distinguishing between stable and experimental parts of the codebase.

- **Marking a Class as Experimental**

```csharp
using System;
using System.Diagnostics.CodeAnalysis;

// Marking an entire class as experimental
[Experimental("Exp_Class_001", UrlFormat = "https://docs.example.com/{0}")]
public class ExperimentalClass
{
    public void ExperimentalMethod()
    {
        Console.WriteLine("This method is experimental.");
    }
}

class Program
{
    static void Main()
    {
        // Using the experimental class
        var expClass = new ExperimentalClass();
        expClass.ExperimentalMethod();
    }
}
```

- This code snippet uses the `ExperimentalAttribute` to mark the `ExperimentalClass` as experimental. The attribute includes a diagnostic ID and a URL for documentation. Any usage of this class will trigger a compiler warning unless suppressed.

- **Experimental Method in a Standard Class**

```csharp
using System;
using System.Diagnostics.CodeAnalysis;

public class UtilityClass
{
    // Marking a single method as experimental
    [Experimental("Exp_Method_002")]
    public void ExperimentalFeature()
    {
        Console.WriteLine("This feature is under development.");
    }
}

class Program
{
    static void Main()
    {
        var utility = new UtilityClass();
        utility.ExperimentalFeature(); // Triggers a warning
    }
}
```

- In this example, only the `ExperimentalFeature` method is marked as experimental. When this method is called, the compiler will issue a warning identified by the specified diagnostic ID.

- **Suppressing Warnings for Experimental Features**

```csharp
using System;
using System.Diagnostics.CodeAnalysis;

public class AdvancedClass
{
    [Experimental("Exp_Advanced_003")]
    public void AdvancedMethod()
    {
        Console.WriteLine("Advanced experimental method.");
    }
}

class Program
{
    static void Main()
    {
        // Suppressing the experimental warning
        #pragma warning disable Exp_Advanced_003
        var advanced = new AdvancedClass();
        advanced.AdvancedMethod();
        #pragma warning restore Exp_Advanced_003
    }
}
```

- This snippet demonstrates how to suppress warnings for specific experimental features using the diagnostic ID provided in the `ExperimentalAttribute`. The `#pragma warning disable` directive is used to prevent the warning from being shown during compilation.

### Interceptors

In C# 12.0, the feature known as "Interceptors" is introduced as an experimental capability. This feature allows methods to declaratively intercept and replace calls to other methods at compile time, primarily aimed at enhancing ahead-of-time (AOT) compilation scenarios. It is important to note that this feature is still in a preview stage and may undergo changes or be removed in future releases. Interceptors are designed to be used with source generators, enabling modifications to code during compilation without altering the original source code.

- **Basic Interceptor Usage**: This example shows how to set up a basic interceptor to modify a method's behavior by substituting the original method call with the interceptor's implementation.

```csharp
using System;
using System.Runtime.CompilerServices;

namespace InterceptorDemo
{
    public class Program
    {
        static void Main(string[] args)
        {
            var greeter = new Greeter();
            greeter.Greet("World"); // This call will be intercepted
        }
    }

    public class Greeter
    {
        // Original method that would normally print "Hello, World"
        public void Greet(string name)
        {
            Console.WriteLine($"Hello, {name}");
        }
    }

    // Interceptor class that provides an alternative implementation for Greet method
    public static class GreeterInterceptors
    {
        // The InterceptsLocation attribute specifies which method call to intercept
        // "Program.cs" is the file where the Greeter class is used, and the line number should match where the Greet method is invoked in Main
        [InterceptsLocation("Program.cs", line: 11, character: 13)]
        public static void CustomGreet(this Greeter greeter, string name)
        {
            Console.WriteLine($"Welcome, {name}, to the world of interceptors!");
        }
    }
}
```

- **Interceptor with Condition**: This example demonstrates how to apply conditions within an interceptor to selectively modify behavior.

```csharp
using System;
using System.Runtime.CompilerServices;

namespace InterceptorDemo
{
    public class Program
    {
        static void Main(string[] args)
        {
            var calculator = new Calculator();
            Console.WriteLine($"Sum: {calculator.Add(5, 3)}"); // This call will be intercepted
        }
    }

    public class Calculator
    {
        public int Add(int x, int y)
        {
            return x + y;
        }
    }

    public static class CalculatorInterceptors
    {
        [InterceptsLocation("Program.cs", line: 11, character: 34)]
        public static int InterceptAdd(this Calculator calculator, int x, int y)
        {
            // Only intercepting if both numbers are greater than 1
            if (x > 1 && y > 1)
            {
                Console.WriteLine("Intercepted Add!");
                return x + y + 10; // Adds an extra 10 to the sum as part of the interception
            }

            return calculator.Add(x, y); // Calls the original method if condition is not met
        }
    }
}
```

### Collection Expressions

The new feature of "Collection Expressions" in C# 12.0 simplifies the creation of collections. It allows you to use a concise syntax to define arrays, lists, and spans without the need for traditional methods like constructors or initializers. This feature supports various collection types, including basic arrays, and is also compatible with collection initializer types like List<T>. It includes the use of a spread operator, which seamlessly integrates elements from one collection into another. This enhancement not only simplifies the code but makes it cleaner and easier to read.

- **Creating a simple array of integers**

```csharp
int[] numbers = [1, 2, 3, 4, 5];
// This initializes an array with five elements. The new syntax uses brackets instead of curly braces and does not require the 'new' keyword.
```

- **Combining multiple collections into one using the spread operator**

```csharp
int[] moreNumbers = [0, ..numbers, 6];
// This combines the elements of 'numbers' array with new elements 0 and 6. The '..' syntax spreads the elements of 'numbers' into the new array.
```

- **Creating a list of strings**

```csharp
List<string> colors = ["red", "green", "blue"];
// Here, a List of strings is initialized with three colors. This utilizes the collection initializer feature directly without needing 'new List<string>()'.
```

- **Creating a Span of characters**

```csharp
Span<char> alphabetSpan = ['a', 'b', 'c'];
// This creates a Span of characters directly from the given elements. Spans are more efficient in certain scenarios like slicing without allocations.
```

- **Creating a jagged array using nested collection expressions**

```csharp
int[][] jaggedArray = [[1, 2], [3, 4, 5]];
// Jagged arrays are arrays of arrays. Here, the outer array contains two inner arrays with elements initialized using the new collection expression syntax.
```

- **Using variables and combining them in new collections**

```csharp
var firstPart = [1, 2, 3];
var secondPart = [4, 5];
int[] combinedArray = [..firstPart, ..secondPart];
// Variables 'firstPart' and 'secondPart' are spread into 'combinedArray' to create a single concatenated array of integers.
```

- **Implicit type arrays with mixed types using collection expressions**

```csharp
object[] mixedTypes = ["string", 10, true];
// This array can hold objects of any type, demonstrating the versatility of collection expressions with automatic type inference.
```

## Conclusion

C# 12.0 marks a significant evolution in the language, focusing on simplifying syntax, enhancing performance, and improving code maintainability. With the introduction of Primary Constructors, Inline Arrays, and Default Lambda Parameters, developers can write more concise and expressive code. Features like ref readonly parameters and the ability to alias any type with using further extend the language's versatility and safety. The addition of Collection Expressions and spread operators simplifies the handling of data collections, making the codebase cleaner and easier to manage.

Moreover, experimental features such as the Experimental Attribute and Interceptors provide a sandbox for innovation, allowing developers to experiment with new concepts before fully integrating them into production code. These advancements not only enhance developer productivity but also encourage safer and more efficient coding practices. As C# continues to evolve, these features are expected to be refined and expanded, offering even greater possibilities for future development projects.
