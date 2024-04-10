# C# 11.0 New Features

## Table of Contents

- [C# 11.0 New Features](#c-110-new-features)
  - [Table of Contents](#table-of-contents)
  - [Introduction and Overview](#introduction-and-overview)
  - [New Features](#new-features)
    - [Generic attributes](#generic-attributes)
    - [Generic math support](#generic-math-support)
    - [Numeric IntPtr and UIntPtr](#numeric-intptr-and-uintptr)
    - [Newlines in string interpolations](#newlines-in-string-interpolations)
    - [Raw string literals](#raw-string-literals)
    - [UTF-8 string literals](#utf-8-string-literals)
    - [List patterns](#list-patterns)
    - [File-local types](#file-local-types)
    - [Required members](#required-members)
    - [Auto-default structs](#auto-default-structs)
    - [Pattern match `Span<char>` on a constant string](#pattern-match-spanchar-on-a-constant-string)
    - [Extended `nameof` scope](#extended-nameof-scope)
    - [`ref` fields and `scoped ref`](#ref-fields-and-scoped-ref)
    - [Improved method group conversion to delegate](#improved-method-group-conversion-to-delegate)
    - [Warning wave 7](#warning-wave-7)
  - [Conclusion](#conclusion)

## Introduction and Overview

In C# 11.0, the language continues its trajectory of becoming more powerful, flexible, and efficient for developers. This version introduces a number of new features aimed at enhancing the programming experience, ensuring type safety, and improving the performance of C# applications. Among these features, generic attributes stand out for providing a more convenient syntax for attributes that require a `System.Type` parameter, simplifying the declaration and use of attributes in your code. Generic math support opens up new possibilities for performing mathematical operations on generic types, enhancing the language's capabilities for scientific and numerical computing.

Furthermore, the introduction of numeric `IntPtr` and `UIntPtr` types facilitates operations that involve pointer-sized integers, making low-level programming tasks more straightforward. The ability to include newlines in string interpolations and the addition of raw string literals significantly improve the readability and maintainability of code that involves complex string manipulations, such as SQL queries or JSON strings. UTF-8 string literals address the common need for UTF-8 encoding in applications, simplifying the handling of text data in a globalized world.

List patterns extend pattern matching to sequences, making it easier to work with arrays and lists in a more expressive manner. File-local types help avoid naming conflicts, especially in larger projects or when using source generators, by limiting type visibility to the file they are declared in. Required members enforce initialization, ensuring that objects are fully and correctly initialized upon creation. Auto-default structs enhance the safety and usability of structs by ensuring all fields are initialized. The ability to pattern match `Span<char>` on a constant string and the extended `nameof` scope further contribute to the language's expressiveness and safety.

Additionally, the introduction of `ref` fields and `scoped ref`, along with improved method group conversion to delegate, offer advanced capabilities for high-performance and memory-efficient programming. Warning wave 7 continues the effort to improve code quality by identifying potential issues in code patterns. These enhancements in C# 11.0 reflect a balanced approach to language evolution, catering to both high-level application development and low-level system programming needs, making C# an even more versatile and powerful programming language.

## New Features

### Generic attributes

The "Generic attributes" feature in C# 11.0 allows developers to use generic classes as attributes. Before this update, attributes in C# could not have generic types, limiting their flexibility. This meant developers had to create attributes with a specific type or use workarounds, such as passing a `Type` object to the attribute's constructor. With C# 11.0, you can directly declare an attribute class as generic. This enhancement simplifies the syntax for attributes that need a `System.Type` parameter, making the code cleaner and more intuitive. For example, instead of defining an attribute that takes a `Type` in its constructor, you can now create a generic attribute class and specify the type when applying the attribute. This new feature is particularly useful for scenarios where the attribute needs to work with a specific type that is known at compile time.

```csharp
using System;

// Define a generic attribute by specifying a generic type parameter.
// This attribute can now be applied to various code elements, specifying different types for each application.
public class GenericInfoAttribute<T> : Attribute
{
    // This property holds a piece of information about the type T.
    public string Info { get; }

    // Constructor for the attribute, taking a string that represents information about the type T.
    public GenericInfoAttribute(string info)
    {
        Info = info;
    }
}

// Example usage of the newly defined generic attribute.
// Here, we apply the attribute to a class and specify `string` as the generic type parameter.
// The provided string describes the use or characteristics of the type parameter.
[GenericInfo<string>("This class performs operations on strings.")]
public class StringProcessor
{
    // A method that would perform some operations related to strings.
    public void Process(string input)
    {
        Console.WriteLine($"Processing string: {input}");
    }
}

// Another example, applying the same attribute to a different class.
// This time we specify `int` as the generic type parameter, indicating a different use or characteristic.
[GenericInfo<int>("This class handles mathematical operations on integers.")]
public class MathProcessor
{
    // A method that performs some mathematical operations on integers.
    public void Process(int number)
    {
        Console.WriteLine($"Processing number: {number}");
    }
}

// A demonstration of how to retrieve and use the information from the applied generic attributes.
class Program
{
    static void Main()
    {
        // Use reflection to get the attributes applied to the StringProcessor class.
        var stringAttribute = (GenericInfoAttribute<string>)Attribute.GetCustomAttribute(typeof(StringProcessor), typeof(GenericInfoAttribute<string>));
        Console.WriteLine($"Attribute on StringProcessor: {stringAttribute.Info}");

        // Similarly, retrieve the attribute for the MathProcessor class.
        var intAttribute = (GenericInfoAttribute<int>)Attribute.GetCustomAttribute(typeof(MathProcessor), typeof(GenericInfoAttribute<int>));
        Console.WriteLine($"Attribute on MathProcessor: {intAttribute.Info}");
    }
}
```

### Generic math support

The "Generic math support" feature introduced in C# 11.0 enhances the ability to perform mathematical operations on generic types. This feature revolves around several key improvements:

- **Static Virtual Members in Interfaces**: Allows defining interfaces with static abstract or static virtual members. This includes mathematical operators, enabling the creation of generic types that can use these operators. For example, it enables the definition of a generic interface for addition operations.

- **Checked User Defined Operators**: Enables developers to define their own arithmetic operators that are checked for overflow by default, improving safety in numerical computations.

- **Relaxed Shift Operator Requirements**: The requirement that the second operand of a shift operator must be an `int` or implicitly convertible to `int` has been removed. This change enhances flexibility in shift operations for generic math.

- **Unsigned Right-Shift Operator**: A new `>>>` operator for unsigned right shifts allows for more intuitive and clear bitwise operations without needing to cast to an unsigned type first.

These features together enable more expressive and type-safe mathematical operations in generic programming scenarios, making it easier to work with numbers in a generic context.

```csharp
// Define a generic interface for addition. This interface uses static abstract members, a new feature in C# 11.0.
public interface IAdditionOperators<TSelf, TOther, TResult>
    where TSelf : IAdditionOperators<TSelf, TOther, TResult>
{
    // Define a static abstract method for addition. This method signature enables addition operations on generic types.
    static abstract TResult Add(TSelf left, TOther right);
}

// Implement the interface for a custom numeric type.
public struct CustomNumber : IAdditionOperators<CustomNumber, CustomNumber, CustomNumber>
{
    private double value;

    public CustomNumber(double value)
    {
        this.value = value;
    }

    // Implement the Add method as specified by the interface.
    public static CustomNumber Add(CustomNumber left, CustomNumber right)
    {
        return new CustomNumber(left.value + right.value);
    }

    public override string ToString() => value.ToString();
}

// Define a generic method that computes the sum of an array of elements.
// The constraint ensures that this method can only be used with types that implement IAdditionOperators.
public static T Sum<T>(T[] array) where T : IAdditionOperators<T, T, T>
{
    // Initialize the sum with the default value of T. Assumes a parameterless constructor or default struct behavior.
    T sum = default;

    // Iterate over the array and accumulate the sum using the Add method defined by the IAdditionOperators interface.
    foreach (var item in array)
    {
        sum = T.Add(sum, item);
    }

    return sum;
}

// Usage example:
public class Program
{
    public static void Main()
    {
        // Create an array of CustomNumber instances.
        CustomNumber[] numbers = { new CustomNumber(1.5), new CustomNumber(2.5), new CustomNumber(3) };

        // Calculate the sum of the CustomNumber instances using the generic Sum method.
        var sum = Sum(numbers);

        // Output the result.
        Console.WriteLine($"The sum is: {sum}");
    }
}
```

- The `IAdditionOperators<TSelf, TOther, TResult>` interface leverages the static abstract members feature of C# 11.0 to define a generic addition operation.
- `CustomNumber` struct implements this interface, providing a concrete addition operation for its type.
- The generic `Sum<T>` method demonstrates how to perform operations on generic types constrained to implement the `IAdditionOperators` interface. It calculates the sum of elements in an array, showing the flexibility and power of generic math support in C# 11.0.
- Finally, the `Main` method illustrates using these concepts to sum an array of `CustomNumber` instances, highlighting the practical application of generic math support in real-world scenarios.

### Numeric IntPtr and UIntPtr

The enhancement of "Numeric IntPtr and UIntPtr" in C# 11.0 introduces the use of `nint` and `nuint` as aliases for `System.IntPtr` and `System.UIntPtr`, respectively. This feature streamlines operations that involve pointer-sized integers by providing a more intuitive way to represent them directly in code, without the need to explicitly refer to the more cumbersome `System.IntPtr` and `System.UIntPtr` types. It aligns with the evolution of C# towards supporting modern, safe, and efficient low-level programming, especially in scenarios requiring compatibility with native code or handling memory addresses.

```csharp
using System;

class Program
{
    static void Main()
    {
        // Example of using nint (numeric IntPtr) for arithmetic operations
        // Initialization of nint variables with pointer-sized integers
        nint firstValue = 5;
        nint secondValue = 10;

        // Performing arithmetic operations with nint
        nint sum = firstValue + secondValue; // Addition
        nint difference = secondValue - firstValue; // Subtraction
        nint product = firstValue * secondValue; // Multiplication
        nint quotient = secondValue / firstValue; // Division

        Console.WriteLine($"Sum: {sum}, Difference: {difference}, Product: {product}, Quotient: {quotient}");

        // Example of using nuint (numeric UIntPtr) for arithmetic operations
        // Similar to nint but for unsigned integer pointer-sized operations
        nuint uFirstValue = 15;
        nuint uSecondValue = 25;

        // Arithmetic operations with nuint
        nuint uSum = uFirstValue + uSecondValue; // Unsigned addition
        nuint uDifference = uSecondValue - uFirstValue; // Unsigned subtraction
        nuint uProduct = uFirstValue * uSecondValue; // Unsigned multiplication
        nuint uQuotient = uSecondValue / uFirstValue; // Unsigned division

        Console.WriteLine($"Unsigned Sum: {uSum}, Unsigned Difference: {uDifference}, Unsigned Product: {uProduct}, Unsigned Quotient: {uQuotient}");

        // Demonstrating the use of nint and nuint in memory-related operations
        // Allocating a block of memory of size equal to the product calculated above
        nint size = product; // Assuming product is the required size for memory allocation
        IntPtr memoryAddress = new IntPtr(size);

        Console.WriteLine($"Memory address allocated with size {size}: {memoryAddress}");

        // Similar operation with nuint
        nuint uSize = uProduct; // Using unsigned product as size for memory allocation
        UIntPtr uMemoryAddress = new UIntPtr(uSize);

        Console.WriteLine($"Unsigned memory address allocated with size {uSize}: {uMemoryAddress}");

        // Note: These examples demonstrate the ease of using nint and nuint for numeric operations
        // that involve memory management or interaction with native code in C# 11.0.
        // The simplicity and readability of code are significantly improved with these types.
    }
}
```

### Newlines in string interpolations

In C# 11.0, the enhancement to string interpolations allows you to include newlines directly within the curly braces `{}` of an interpolation expression. This improvement makes it simpler to format string interpolations that contain longer C# expressions, like LINQ queries or pattern matching switch expressions. By supporting newlines within these interpolations, C# 11.0 enhances readability and maintainability of code that combines complex logic with string formatting.

- **Incorporating LINQ Query**: This example demonstrates embedding a LINQ query within a string interpolation, showcasing the readability improvement.

```csharp
using System;
using System.Linq;
using System.Collections.Generic;

public class Example
{
    public static void Main()
    {
        var numbers = new List<int> { 1, 2, 3, 4, 5 };

        // Using newlines in string interpolation for a LINQ query
        var result = $@"
            The sum of all numbers: {
                numbers.Sum()
            }
            Average of numbers: {
                numbers.Average()
            }";

        Console.WriteLine(result);
    }
}
```

- **Using a Switch Expression**: Embed a pattern matching switch expression within a string using newlines in string interpolations for clarity.

```csharp
using System;

public class Example
{
    public static void Main()
    {
        var month = 8;

        // Embedding a switch expression with newlines in string interpolation
        var season = $@"
            {month} corresponds to {
                month switch
                {
                    12 or 1 or 2 => "Winter",
                    3 or 4 or 5 => "Spring",
                    6 or 7 or 8 => "Summer",
                    9 or 10 or 11 => "Autumn",
                    _ => "Invalid month"
                }
            }";

        Console.WriteLine(season.Trim());
    }
}
```

- **Complex Expression with LINQ and Conditional Logic**: Combine LINQ with conditional logic within a string interpolation for detailed messages.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Example
{
    public static void Main()
    {
        var temperatures = new Dictionary<string, int>
        {
            {"London", 15},
            {"Paris", 25},
            {"New York", 20}
        };

        var info = $@"
            City temperatures:
            {
                string.Join("
", temperatures.Select(kv =>
                    $"{kv.Key} is {(kv.Value >= 20 ? "warm" : "cool")} at {kv.Value} degrees"))
            }
            Average temperature: {
                temperatures.Values.Average()
            } degrees";

        Console.WriteLine(info.Trim());
    }
}
```

### Raw string literals

The new feature of "Raw string literals" in C# 11.0 allows you to write strings that contain special characters without needing to escape them. This feature is great for dealing with long strings, paths, or text with many special characters, like JSON, making your code cleaner and more readable. You can create a raw string literal by using at least three double quotes (""") to start and end the string. This lets you include new lines, quotes, and other special characters directly in the string without escaping them. It's especially useful when your strings contain paths or multiline text, simplifying how you work with strings in C#​.

- **Multiline JSON String**

```csharp
// Using raw string literals for a multiline JSON string
// No need for escape sequences for double quotes
string json = """
{
    "name": "John Doe",
    "age": 30,
    "isEmployed": true,
    "addresses": [
        {"street": "123 Main St", "city": "Anytown"},
        {"street": "456 Maple Ave", "city": "Somewhere"}
    ]
}
""";
Console.WriteLine(json);
```

- **File Paths**

```csharp
// Using raw string literals for file paths on Windows
// Backslashes do not need to be escaped
string filePath = """C:\Users\JohnDoe\Documents\Report.txt""";
Console.WriteLine(filePath);
```

- **Embedded Quotes**

```csharp
// Using raw string literals to include embedded quotes
// Useful for SQL queries, JSON, or XML strings without escaping quotes
string query = """
SELECT * FROM Users WHERE Name="John 'Johnny' Doe"
""";
Console.WriteLine(query);
```

- **Regular Expressions**

```csharp
// Raw string literals make regular expressions clearer
// No need to escape backslashes, making patterns more readable
string pattern = """\d{3}-\d{2}-\d{4}"""; // Matches a Social Security number pattern
Console.WriteLine(pattern);
```

- **Multi-line Code Snippets**

```csharp
// Raw string literals for embedding multi-line code snippets
// Ideal for tutorials, code generation, or templating scenarios
string codeSnippet = """
public class HelloWorld
{
    public static void Main(string[] args)
    {
        Console.WriteLine("Hello, World!");
    }
}
""";
Console.WriteLine(codeSnippet);
```

### UTF-8 string literals

The new "UTF-8 string literals" feature in C# 11.0 allows developers to use a `u8` suffix on string literals to specify UTF-8 encoding directly in the code. This is particularly useful when dealing with text data that requires UTF-8 encoding, such as when working with web protocols or files that are encoded in UTF-8. The primary advantage of this feature is the simplification of creating UTF-8 encoded strings without the need for additional conversion methods. This enhancement addresses the prevalent use of UTF-8 encoding in applications and systems, providing a more straightforward and performance-efficient way to handle UTF-8 string data in C#.

- **Using UTF-8 String Literals for HTTP Content**: This example shows how UTF-8 string literals can be used to define HTTP request content, which typically requires UTF-8 encoding. This makes the process more straightforward by avoiding explicit encoding steps.

```csharp
using System.Net.Http;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        using var client = new HttpClient();

        // Define UTF-8 string literal with 'u8' suffix for HTTP content
        var content = "{'name':'John', 'age':30, 'city':'New York'}"u8;

        // Create ByteArrayContent with the UTF-8 encoded string
        // Since the string is already in UTF-8 format, no further conversion is required
        var httpContent = new ByteArrayContent(content.ToArray());

        // Set the content type to application/json; charset=UTF-8
        httpContent.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("application/json")
        {
            CharSet = "UTF-8"
        };

        // Post the JSON content to a web API
        var response = await client.PostAsync("https://example.com/api/users", httpContent);

        // Output the response from the server
        Console.WriteLine(await response.Content.ReadAsStringAsync());
    }
}
```

- **Reading and Writing UTF-8 Encoded Files** This example demonstrates reading from and writing to UTF-8 encoded files using UTF-8 string literals, showcasing the seamless handling of UTF-8 data.

```csharp
using System.IO;
using System.Threading.Tasks;

class FileOperations
{
    static async Task WriteAndReadUtf8File()
    {
        var filePath = "example.txt";

        // UTF-8 string literal to write to a file
        var utf8StringToWrite = "This is some UTF-8 encoded text including characters like ñ and ö."u8;

        // Write the UTF-8 encoded string to a file
        // The 'WriteAllBytesAsync' method takes a byte array, which is exactly what the UTF-8 string literal provides
        await File.WriteAllBytesAsync(filePath, utf8StringToWrite.ToArray());

        // Read the UTF-8 encoded file
        // The content is read as bytes and then converted to a string for display
        var bytesRead = await File.ReadAllBytesAsync(filePath);
        var fileContent = System.Text.Encoding.UTF8.GetString(bytesRead);

        // Display the content read from the file
        Console.WriteLine($"Content read from file: {fileContent}");
    }
}
```

### List patterns

The "List patterns" feature in C# 11.0 enhances pattern matching to handle sequences in lists or arrays more efficiently. This feature allows for matching sequences of elements, including checking if a sequence is empty, has specific elements, or matches a range of elements. It simplifies checking list contents without manually iterating through each element, making code cleaner and more expressive. For example, it supports matching elements with various patterns such as constant, type, and relational patterns, and introduces a discard pattern (\_) for any single element and a new range pattern (..) for any sequence of elements. This feature is particularly useful for scenarios requiring specific list structures or content, enhancing code readability and maintainability by providing a straightforward syntax for complex list checks.

- **Matching Exact Sequence**

```csharp
int[] numbers = { 1, 2, 3 };

// Check if 'numbers' matches exactly the sequence [1, 2, 3]
if (numbers is [1, 2, 3])
{
    Console.WriteLine("The sequence matches [1, 2, 3].");
}
```

This example demonstrates how to match an array against an exact sequence of elements. The pattern `[1, 2, 3]` directly matches the array `numbers`.

- **Matching With Discard Pattern**

```csharp
int[] numbers = { 1, 2, 3, 4 };

// Check if 'numbers' starts with 1 and ends with 4, ignoring the middle elements
if (numbers is [1, .., 4])
{
    Console.WriteLine("Starts with 1 and ends with 4.");
}
```

The `..` operator is used here to ignore any number of elements in the middle, focusing only on the start and end of the sequence.

- **Extracting Subsequences**

```csharp
int[] numbers = { 0, 1, 2, 3, 4 };

// Extract the subsequence between the first and last element
if (numbers is [0, ..var middle, 4])
{
    Console.WriteLine($"Middle sequence: {string.Join(", ", middle)}");
}
```

This example uses the `..var` pattern to extract and store a subsequence (`middle`) from the array, excluding the first and last elements.

- **Matching With Conditions**

```csharp
int[] numbers = { 1, 2, 3, 4, 5 };

// Check if 'numbers' starts with a value less than 5 followed by any sequence, and ends with 5
if (numbers is [<5, .., 5])
{
    Console.WriteLine("Starts with a value less than 5 and ends with 5.");
}
```

This code checks for a sequence starting with any value less than 5 and ending with 5, showcasing relational patterns within list patterns.

- **Nested List Patterns**

```csharp
int[][] matrix = { new[] { 1, 2 }, new[] { 3, 4 } };

// Check if 'matrix' contains an array starting with 1 and another array starting with 3
if (matrix is [[1, ..], [3, ..]])
{
    Console.WriteLine("Matrix contains the specified sequences.");
}
```

Nested list patterns allow for matching complex sequences, such as arrays of arrays, based on specific criteria for each subsequence.

### File-local types

In C# 11.0, the introduction of file-local types allows developers to define types with a visibility scope limited to the file they are declared in. This feature is especially useful for source generator authors, as it helps prevent naming conflicts by ensuring that the types they generate do not interfere with types defined elsewhere in the application. By using the `file` access modifier, you can create a class, struct, or other type that is only accessible within the same source file. This makes your code cleaner and more modular, particularly when dealing with generated code that should not be exposed outside of its defining context.

```csharp
// Example demonstrating the use of file-local types in C# 11.0

// File: DataAccessLayer.cs
// This file includes a file-local type `DatabaseConnection`. This type is scoped to this file only,
// meaning it cannot be accessed or instantiated outside of this file. This is useful for internal
// implementation details that should not be exposed to the rest of the application.
file class DatabaseConnection
{
    // Private constructor ensures that instances of DatabaseConnection can only
    // be created within this file, further encapsulating the database connection logic.
    private DatabaseConnection() { }

    // Method to establish a connection to the database.
    public void Connect()
    {
        // Implementation of the connection logic...
        Console.WriteLine("Database connection established.");
    }

    // Factory method to create instances of DatabaseConnection.
    // This allows for controlled creation and potentially initialization of the connection.
    public static DatabaseConnection CreateConnection()
    {
        return new DatabaseConnection();
    }
}

// Public class that represents the data access layer of an application.
// This class can use the DatabaseConnection type even though it's file-local,
// because it's defined in the same file.
public class DataAccess
{
    public void AccessDatabase()
    {
        // Using the factory method to create a database connection.
        var connection = DatabaseConnection.CreateConnection();
        // Establishing the connection to the database.
        connection.Connect();
    }
}

// File: Program.cs
// This file attempts to use the DatabaseConnection class defined in DataAccessLayer.cs.
// However, since DatabaseConnection is a file-local type, it won't be accessible from here,
// and the following code will result in a compilation error if uncommented.

/*
public class Program
{
    static void Main(string[] args)
    {
        // Attempt to directly access DatabaseConnection will fail at compile time.
        var connection = new DatabaseConnection(); // This line will cause a compile error.
    }
}
*/

// The file-local modifier helps encapsulate types that are meant for internal use within a file,
// reducing the surface area of APIs and minimizing potential naming conflicts across large projects.
```

### Required members

In C# 11.0, a new feature called "Required Members" was introduced to improve how developers define and work with class or struct members, particularly regarding initialization. This feature allows you to mark fields or properties with the `required` modifier, indicating that they must be initialized when an object is created. The goal is to help ensure that objects are fully initialized after construction, reducing the risk of runtime errors due to uninitialized members. This feature is part of the broader efforts to enhance type safety and object initialization clarity in C#. It applies to fields and properties in classes and structs, enforcing initialization either through constructors or object initializers, thus fostering better coding practices by making initialization requirements explicit and compiler-verified.

- **Basic Usage of Required Members in a Class**

```csharp
public class Employee
{
    // Marking FirstName and LastName as required ensures they must be initialized
    // when an instance of Employee is created.
    public required string FirstName { get; init; }
    public required string LastName { get; init; }

    // Optional property; does not need to be initialized immediately.
    public string? MiddleName { get; init; }

    // Example method to demonstrate usage of required properties.
    public void PrintFullName()
    {
        Console.WriteLine($"{FirstName} {MiddleName} {LastName}");
    }
}

public class Program
{
    public static void Main()
    {
        // Correct initialization: All required members are initialized.
        var employee = new Employee { FirstName = "John", LastName = "Doe" };
        employee.PrintFullName();

        // Incorrect initialization: Compiler error due to missing LastName initialization.
        // var employeeError = new Employee { FirstName = "John" }; // This line will cause a compiler error.
    }
}
```

- **Required Members with Constructor**

```csharp
public class Product
{
    // Required members can also be initialized via a constructor.
    public required string Name { get; init; }
    public required decimal Price { get; init; }

    // Constructor that initializes the required members.
    [SetsRequiredMembers]
    public Product(string name, decimal price)
    {
        Name = name;
        Price = price;
    }

    // Method to display product information.
    public void DisplayInfo()
    {
        Console.WriteLine($"Product: {Name}, Price: {Price}");
    }
}

public class Program
{
    public static void Main()
    {
        // Creating and initializing Product using the constructor.
        var product = new Product(name: "Laptop", price: 1200.00m);
        product.DisplayInfo();
    }
}
```

- **Required Members in Inheritance**

```csharp
public class Person
{
    public required string FirstName { get; init; }
    public required string LastName { get; init; }
}

public class Student : Person
{
    // Additional required member specific to Student.
    public required int StudentID { get; init; }

    public void PrintStudentInfo()
    {
        // Access inherited required properties and new required property.
        Console.WriteLine($"Student: {FirstName} {LastName}, ID: {StudentID}");
    }
}

public class Program
{
    public static void Main()
    {
        // Initializing a Student instance, including all required members from the base and derived class.
        var student = new Student { FirstName = "Alice", LastName = "Johnson", StudentID = 12345 };
        student.PrintStudentInfo();
    }
}
```

### Auto-default structs

The "Auto-default structs" feature in C# 11.0 makes sure all fields of a struct are initialized to their default values when a constructor is executed. This means if a constructor doesn't set all fields, the compiler will automatically set any unset fields to their default values. This enhancement simplifies struct creation by ensuring no fields are left uninitialized, which helps avoid common errors and makes structs safer and easier to use.

- **Basic Usage of Auto-default Structs**

```csharp
// Define a struct with two fields
public struct Point
{
    public int X; // Will be auto-defaulted if not set in constructor
    public int Y; // Will be auto-defaulted if not set in constructor

    // Constructor that sets X but not Y
    public Point(int x)
    {
        X = x;
        // Y is not set here, so C# 11 automatically initializes it to default(int), which is 0
    }
}

// Demonstrates the use of the struct
public class Program
{
    public static void Main()
    {
        var p = new Point(10);
        Console.WriteLine($"X: {p.X}, Y: {p.Y}"); // Output will be "X: 10, Y: 0"
        // Note: Y is automatically set to 0 by the compiler even though it was not explicitly set in the constructor
    }
}
```

- **Auto-default With Properties**

```csharp
// Define a struct with properties
public struct Rectangle
{
    public double Width { get; set; } // Auto-properties are also auto-defaulted
    public double Height { get; set; }

    // Constructor that sets Width but not Height
    public Rectangle(double width)
    {
        Width = width;
        // Height is not set, so it will be automatically initialized to default(double), which is 0.0
    }
}

// Demonstrates the use of the struct with properties
public class Program
{
    public static void Main()
    {
        var r = new Rectangle(4.5);
        Console.WriteLine($"Width: {r.Width}, Height: {r.Height}"); // Output will be "Width: 4.5, Height: 0.0"
        // Note: Height is automatically set to 0.0 by the compiler even though it was not explicitly set in the constructor
    }
}
```

### Pattern match `Span<char>` on a constant string

In C# 11.0, there's a new feature that allows you to pattern match `Span<char>` or `ReadOnlySpan<char>` against a constant string. This enhancement means you can now use pattern matching, a powerful feature for checking the structure of data, with spans of characters. Simply put, if you have a sequence of characters (a span) and want to see if it matches a specific text, you can do this directly. This makes your code cleaner and more straightforward when working with spans that represent strings, without needing to convert them back to string objects for comparison. This feature expands the versatility of pattern matching in C#, allowing for more efficient and readable code when dealing with text processing scenarios.

- **Simple Pattern Matching with `Span<char>`**: This example demonstrates checking if a `Span<char>` directly matches a constant string.

```csharp
using System;

class Program
{
    static void Main()
    {
        // Original string
        string greeting = "Hello, World!";

        // Converting the string to a Span<char>
        Span<char> spanGreeting = greeting.AsSpan();

        // Using pattern matching to check if the span matches a constant string
        if (spanGreeting is "Hello, World!")
        {
            Console.WriteLine("The greeting matches 'Hello, World!'");
        }
        else
        {
            Console.WriteLine("The greeting does not match.");
        }
    }
}
```

- **Using Pattern Matching in Methods**: This example shows how to use pattern matching with `Span<char>` in method parameters, enabling efficient text processing.

```csharp
using System;

class SpanPatternMatching
{
    // Method that checks if the input Span<char> matches certain patterns
    public static void CheckGreeting(Span<char> input)
    {
        if (input is "Hello, World!")
        {
            Console.WriteLine("Input is a standard greeting.");
        }
        else if (input is "Hi!")
        {
            Console.WriteLine("Input is a short greeting.");
        }
        else
        {
            Console.WriteLine("Input is an unrecognized greeting.");
        }
    }

    static void Main()
    {
        string message = "Hi!";
        CheckGreeting(message.AsSpan()); // Converts string to Span<char> and checks it
    }
}
```

- **Advanced Use Case with Slicing**: Demonstrates checking for a pattern at the beginning of a span and then slicing the span for further processing.

```csharp
using System;

class AdvancedSpanMatching
{
    // Analyze the content of a Span<char> assuming it starts with a certain pattern
    public static void AnalyzeContent(Span<char> content)
    {
        // Check if the content starts with "Data:"
        if (content.StartsWith("Data:".AsSpan()))
        {
            // Slice the span to exclude the "Data:" part
            Span<char> actualContent = content.Slice(5);

            Console.WriteLine($"Content after 'Data:' - {new string(actualContent)}");
        }
        else
        {
            Console.WriteLine("Content does not start with 'Data:'.");
        }
    }

    static void Main()
    {
        string rawData = "Data:Important Information";
        AnalyzeContent(rawData.AsSpan());
    }
}
```

### Extended `nameof` scope

The "Extended nameof scope" feature in C# 11.0 allows the `nameof` operator to access the names of type parameters and method parameters within the context of attribute declarations on methods or parameters. This enhancement is particularly useful for adding attributes related to nullable analysis, among other scenarios, enabling more expressive and type-safe code especially when dealing with attributes. In simpler terms, you can now use `nameof` to directly reference method parameters and type parameters within attributes applied to those methods or parameters, improving code clarity and reducing the chance of errors caused by typos or changes to parameter names.

- **Using `nameof` with Method Parameters in Attributes**:

Before C# 11.0, referencing a method parameter name within an attribute applied to that method required manually typing the parameter name as a string, which was error-prone.

```csharp
public class ValidateParameterAttribute : Attribute
{
    public string ParameterName { get; }

    public ValidateParameterAttribute(string parameterName)
    {
        ParameterName = parameterName;
    }
}

public class Example
{
    // Before C# 11.0: Manually typing the parameter name as a string
    [ValidateParameter("parameter")]
    public void MethodBefore(string parameter) { }
}
```

With C# 11.0's extended nameof scope, you can directly reference the parameter name, enhancing maintainability.

```csharp
public class ExampleCSharp11
{
    // Using nameof to safely reference the parameter name
    [ValidateParameter(nameof(parameter))]
    public void MethodWithExtendedNameof(string parameter) { }
}
```

- **Enhancing Nullable Analysis with `nameof`**

Referencing method parameters directly in attributes can make code more type-safe and clear, especially for nullable analysis.

```csharp
// Attribute used to indicate a method parameter should not be null
public class NotNullAttribute : Attribute
{
    public string ParameterName { get; }

    public NotNullAttribute(string parameterName)
    {
        ParameterName = parameterName;
    }
}

public class SafetyExample
{
    // Directly referencing the parameter for nullability checks
    [NotNull(nameof(value))]
    public void SafeMethod(string value)
    {
        if (value == null)
            throw new ArgumentNullException(nameof(value));
    }
}
```

### `ref` fields and `scoped ref`

In C# 11.0, a significant enhancement includes the introduction of "ref fields" and `scoped ref` This allows developers to declare ref fields within a ref struct, supporting types like System.Span<T> without the need for special attributes or hidden internal types. Additionally, the `scoped` modifier can be added to any `ref` declaration, limiting the scope where the reference can escape to. This feature enhances the management of memory and the safety of references within high-performance applications by ensuring that references do not outlive their intended scope, thus preventing common mistakes related to memory access and lifetime management in more complex applications.

- **Using `ref fields` in a `ref struct`**: This example demonstrates how to declare and use `ref fields` within a `ref struct`. This feature is crucial for structures that need to wrap references to other memory locations efficiently, such as spans or custom memory management structures.

```csharp
public ref struct RefStructWrapper
{
    // Declare a ref field within a ref struct.
    // This allows the struct to directly reference another piece of memory.
    public ref int RefField;

    // Constructor to initialize the ref field.
    // The ref field allows this structure to directly point to an existing memory location.
    public RefStructWrapper(ref int refToSomeValue)
    {
        RefField = ref refToSomeValue;
    }

    // A method to modify the referenced value.
    // This demonstrates how ref fields can be used to manipulate the data they point to.
    public void IncrementRefFieldValue()
    {
        RefField++;
    }
}

// Example usage:
int originalValue = 10;
RefStructWrapper wrapper = new RefStructWrapper(ref originalValue);
wrapper.IncrementRefFieldValue();
Console.WriteLine(originalValue);  // Outputs: 11, demonstrating the direct modification through the ref field.
```

- **Using `scoped ref`**: This example illustrates the use of `scoped ref` to limit the scope in which a reference can be used. This feature is essential for ensuring that references do not escape their intended scope, thereby preventing issues related to the lifetime of references.

```csharp
public class RefScopeExample
{
    // Method demonstrating the use of scoped ref.
    // The scoped ref ensures that the reference cannot be stored or used outside this method, preventing unintended memory access outside its lifetime.
    public void ProcessData()
    {
        int data = 5;

        // Scoped ref limits the escape scope of the reference to 'data'.
        ScopedRefMethod(scoped ref data);

        // Data can still be used here safely.
        Console.WriteLine(data);  // Outputs: 6, after modification in ScopedRefMethod.
    }

    // A helper method that accepts a scoped ref parameter.
    // This parameter cannot be stored or returned, ensuring its use is confined to this method's scope.
    private void ScopedRefMethod(scoped ref int refParameter)
    {
        // Directly modify the referenced data.
        refParameter++;

        // Attempting to store 'refParameter' in a field or returning it would result in a compile-time error.
    }
}

// Example usage:
var example = new RefScopeExample();
example.ProcessData();
```

### Improved method group conversion to delegate

This enhancement simplifies how developers work with method groups and delegates. Previously, every time a method group was converted to a delegate type, a new delegate instance was created. However, C# 11.0 allows for the reuse of delegate instances in certain scenarios, reducing overhead and improving performance. Specifically, the compiler can now cache delegate objects created from method group conversions and reuse them, rather than creating a new delegate each time. This change makes your code more efficient, especially in scenarios where delegate instances are frequently created from method groups.

- **Before C# 11.0**: Each call to `Where` method with a method group conversion would result in a new delegate instance being created, potentially leading to increased memory usage in high-performance scenarios.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main(string[] args)
    {
        var numbers = new List<int> { 1, 2, 3, 4, 5 };

        // Before C# 11.0: Each call to Where method with a method group conversion
        // would result in a new delegate instance being created.
        // This could lead to increased memory usage in high-performance scenarios.
        var evenNumbersBefore = numbers.Where(IsEven).ToList();

        // Output the results
        Console.WriteLine("Even Numbers (Before C# 11.0): " + string.Join(", ", evenNumbersBefore));
    }

    // A simple method to check if a number is even
    static bool IsEven(int number)
    {
        return number % 2 == 0;
    }
}
```

- **C# 11.0 Improvement**: The compiler may cache the delegate instance created from the method group conversion, reusing it across calls. This can lead to better performance and reduced memory usage.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main(string[] args)
    {
        var numbers = new List<int> { 1, 2, 3, 4, 5 };

        // C# 11.0 Improvement: The compiler may cache the delegate instance created
        // from the method group conversion, reusing it across calls. This can lead to
        // better performance and reduced memory usage.
        var evenNumbersAfter = numbers.Where(IsEven).ToList();

        // Output the results
        Console.WriteLine("Even Numbers (C# 11.0 and After): " + string.Join(", ", evenNumbersAfter));
    }

    // A simple method to check if a number is even
    static bool IsEven(int number)
    {
        return number % 2 == 0;
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main(string[] args)
    {
        var numbers = new List<int> { 1, 2, 3, 4, 5 };

        // Before C# 11.0: Each call to Where method with a method group conversion
        // would result in a new delegate instance being created.
        // This could lead to increased memory usage in high-performance scenarios.
        var evenNumbersBefore = numbers.Where(IsEven).ToList();

        // C# 11.0 Improvement: The compiler may cache the delegate instance created
        // from the method group conversion, reusing it across calls. This can lead to
        // better performance and reduced memory usage.
        var evenNumbersAfter = numbers.Where(IsEven).ToList();

        // Output the results
        Console.WriteLine("Even Numbers (Before C# 11.0): " + string.Join(", ", evenNumbersBefore));
        Console.WriteLine("Even Numbers (C# 11.0 and After): " + string.Join(", ", evenNumbersAfter));
    }

    // A simple method to check if a number is even
    static bool IsEven(int number)
    {
        return number % 2 == 0;
    }
}
```

### Warning wave 7

The "Warning wave 7" feature in C# 11.0 represents an enhancement aimed at improving code quality and maintainability. It doesn't add new syntax or language constructs but focuses on enhancing the compiler's ability to detect potential issues in code. Specifically, it involves the compiler emitting warnings for code patterns that, while legal, may lead to bugs or are considered bad practice. The exact details of what scenarios "Warning wave 7" covers are not explicitly mentioned in the uploaded articles, blogs, and notes. However, this feature is part of the ongoing effort to make C# safer and more robust by encouraging developers to write cleaner, more reliable code. It builds on previous "warning waves" by introducing new warnings that the compiler can emit to highlight areas of code that might need attention or could be improved for better performance, readability, or maintainability.

- **Using Nullable Reference Types**

```csharp
// C# 11.0 encourages the use of nullable reference types to make code more null-safe.
// Assuming Warning Wave 7 includes more comprehensive checks for nullable reference usage.
// Enable nullable context for this file.
#nullable enable

public class Person
{
    // Warning wave 7 might introduce warnings for uninitialized non-nullable properties.
    public string Name { get; set; } // Warning: Non-nullable property 'Name' must be initialized.

    // Correct usage: Initialize non-nullable properties or make them nullable if they are not guaranteed to be initialized.
    public string? OptionalNickname { get; set; } // No warning: Nullable property can be uninitialized.
}

public class Program
{
    public static void Main()
    {
        // Warning wave 7 might introduce more strict analysis on possible null dereference.
        Person person = new Person();
        Console.WriteLine(person.Name.Length); // Warning: Possible null reference exception.

        // Correct usage: Check for null before dereferencing.
        if (person.Name != null)
        {
            Console.WriteLine(person.Name.Length); // No warning: Null check has been performed.
        }
    }
}
```

- **Pattern Matching Enhancements**

```csharp
// Assuming Warning Wave 7 introduces warnings for less efficient pattern matching that can be simplified.
public class Animal { }
public class Dog : Animal { }
public class Cat : Animal { }

public class Program
{
    public static void PrintAnimalType(Animal animal)
    {
        // Warning: The pattern matching can be simplified.
        if (animal is Dog)
        {
            Console.WriteLine("It's a dog!");
        }
        else if (animal is Cat)
        {
            Console.WriteLine("It's a cat!");
        }

        // Correct usage: Use switch expression for more concise and readable pattern matching.
        var message = animal switch
        {
            Dog => "It's a dog!",
            Cat => "It's a cat!",
            _ => "It's some other animal."
        };
        Console.WriteLine(message); // No warning: Pattern matching is concise and efficient.
    }
}
```

- **Definite Assignment Analysis Improvements**

```csharp
public class Program
{
    public static void Process(int? maybeNumber)
    {
        // Assuming Warning Wave 7 improves definite assignment analysis.
        int number;

        // Warning: 'number' may not be definitely assigned before accessing.
        Console.WriteLine(number);

        // Correct usage: Ensure all paths leading to variable access initialize the variable.
        if (maybeNumber.HasValue)
        {
            number = maybeNumber.Value;
        }
        else
        {
            number = 0; // Ensure 'number' is definitely assigned.
        }

        Console.WriteLine(number); // No warning: 'number' is definitely assigned before accessing.
    }
}
```

## Conclusion

C# 11.0 marks a significant advancement in the language's development, introducing a suite of features that enhance its flexibility, safety, and efficiency. With additions like generic attributes, generic math support, and improvements to string handling through raw string literals and UTF-8 string literals, developers are equipped with more tools for writing clear, concise, and type-safe code. The introduction of list patterns, required members, and file-local types further enhances C#'s capabilities for both high-level and low-level programming, ensuring that C# remains a powerful choice for a wide range of software development projects. These features collectively contribute to making C# 11.0 a more expressive, performant, and versatile language, continuing its evolution to meet the needs of modern software development.
