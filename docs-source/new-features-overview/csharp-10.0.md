# C# 10.0 New Features Overview

## Table of Contents

- [C# 10.0 New Features Overview](#c-100-new-features)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [New Features](#new-features)
    - [Record structs](#record-structs)
    - [Improvements of structure types](#improvements-of-structure-types)
    - [Interpolated string handlers](#interpolated-string-handlers)
    - [Global using directives](#global-using-directives)
    - [File-scoped namespace declaration](#file-scoped-namespace-declaration)
    - [Extended property patterns](#extended-property-patterns)
    - [Improvements on lambda expressions](#improvements-on-lambda-expressions)
    - [Allow const interpolated strings](#allow-const-interpolated-strings)
    - [Record types can seal ToString()](#record-types-can-seal-tostring)
    - [Improved definite assignment](#improved-definite-assignment)
    - [Allow both assignment and declaration in the same deconstruction](#allow-both-assignment-and-declaration-in-the-same-deconstruction)
    - [Allow AsyncMethodBuilder attribute on methods](#allow-asyncmethodbuilder-attribute-on-methods)
    - [CallerArgumentExpression attribute](#callerargumentexpression-attribute)
    - [Enhanced #line pragma](#enhanced-line-pragma)
    - [Warning wave 6](#warning-wave-6)
  - [Conclusion](#conclusion)

## Introduction

In C# 10.0, a variety of new features and enhancements have been introduced to further improve the language's capabilities, making it more efficient, expressive, and easier to use for developers. Below is a concise overview of some of the key new features and improvements in C# 10.0:

- **Record Structs**

  - **Introduction**: C# 10.0 introduces `record structs`, expanding the utility of records to value types.
  - **Benefits**: Allows developers to work with immutable value types more easily, supporting value-based equality comparisons and simplifying the syntax for declaring data-centric models.

- **Global Using Directives**

  - **Introduction**: The introduction of global using directives simplifies the management of namespaces across multiple files in a project.
  - **Benefits**: By declaring a `using` directive as global, it applies to all source files in the project, reducing redundancy and improving code organization.

- **File-Scoped Namespace Declaration**

  - **Introduction**: C# 10.0 allows for file-scoped namespaces, reducing the need for additional nesting and braces in files that contain a single namespace.
  - **Benefits**: Streamlines the declaration of namespaces, making the code cleaner and more readable.

- **Extended Property Patterns**

  - **Introduction**: The enhancement of property patterns in C# 10.0 enables more concise and readable code when working with nested properties.
  - **Benefits**: Simplifies pattern matching by allowing direct access to nested properties within patterns.

- **Improved Lambda Expressions**

  - **Introduction**: Lambda expressions in C# 10.0 have seen several improvements, including natural type inference.
  - **Benefits**: Offers more control and flexibility, including the ability to infer the delegate type from the lambda expression itself and the application of attributes directly to lambda expressions.

- **Constant Interpolated Strings**

  - **Introduction**: C# 10.0 introduces the ability to use string interpolation in constant string declarations.
  - **Benefits**: Enhances the readability and expressiveness of constant strings in code, provided all placeholders are themselves constant expressions.

These features collectively contribute to making C# a more powerful and developer-friendly language, offering enhanced capabilities for data modeling, code organization, and expressive coding patterns.

## New Features

### Record structs

The new "Record structs" feature in C# 10.0 brings enhancements to how structures (structs) are used in the language. This feature allows for the declaration of value type records using `record struct` or `readonly record struct` declarations. Unlike traditional structs, record structs provide value-based equality comparisons out of the box, just like record classes introduced in earlier versions of C#. This means you can compare two instances of a record struct, and if their values are the same, the comparison will return true. Additionally, record structs support deconstruction and with-expressions, making them more versatile and concise for certain programming scenarios. This feature expands the utility of records in C#, previously limited to reference types, to now include value types, offering a more consistent and efficient way to work with immutable data structures.

- **Immutable by Default**: Like record classes, record structs are intended to be immutable by default. They provide value semantics for equality.
- **With Expressions**: They support `with` expressions for non-destructive mutation.
- **Parameterless Constructors**: C# 10.0 allows parameterless constructors and property initializers in structs, including record structs.

- **Defining and Using a Record Struct**

```csharp
// Defining a simple record struct
public record struct Person(string FirstName, string LastName);

// Using the record struct
var person1 = new Person("John", "Doe");
Console.WriteLine(person1); // Output: Person { FirstName = John, LastName = Doe }

// Non-destructive mutation with 'with' expression
var person2 = person1 with { FirstName = "Jane" };
Console.WriteLine(person2); // Output: Person { FirstName = Jane, LastName = Doe }
```

_This example demonstrates defining a basic `record struct` and how to use it, including modifying it with a `with` expression._

- **Equality and Hash Code**

- Record structs automatically provide value-based equality checks and hash code generation.

```csharp
var person1 = new Person("John", "Doe");
var person2 = new Person("John", "Doe");

Console.WriteLine(person1 == person2); // Output: True
Console.WriteLine(person1.Equals(person2)); // Output: True
Console.WriteLine(person1.GetHashCode() == person2.GetHashCode()); // Output: True
```

_This example shows that two instances of a `record struct` with the same values are considered equal._

- **Parameterless Constructors and Initializers**

- C# 10.0 allows record structs to have parameterless constructors and property initializers.

```csharp
public record struct Address
{
    public string City { get; init; } = "Unknown";

    public Address() { /* Parameterless constructor */ }
}

// Using the record struct with a default city
var address = new Address();
Console.WriteLine(address.City); // Output: Unknown
```

_This example illustrates using a parameterless constructor and property initializer in a `record struct`._

### Improvements of structure types

In C# 10.0, one notable improvement related to structure types is the enhancement of structure type functionalities. Now, developers can declare an instance parameterless constructor within a structure type. This allows for the initialization of instance fields or properties right at their declaration, making the initialization of structures more flexible and straightforward than before. Moreover, the "with" expression can now be used with any structure type or anonymous (reference) type, expanding the versatility and ease of use of record-like immutability patterns to structures. These enhancements aim to make working with structure types in C# more powerful and convenient by enabling clearer and more concise code for initializing and copying structures​.

- **Parameterless Constructors and Field Initializers**: In C# 10.0, you can now define parameterless constructors in structure types, and you can initialize fields or properties at their declaration. This allows for more flexibility and control over the initialization of structs.

```csharp
public struct Point
{
    // Field initializer
    public double X { get; init; } = 0;
    public double Y { get; init; } = 0;

    // Parameterless constructor
    public Point()
    {
        // You can explicitly set default values
        X = 0;
        Y = 0;
    }
}
```

- This code defines a `Point` struct with a parameterless constructor and field initializers, ensuring that instances of `Point` start with `X` and `Y` set to `0` by default.

- **`with` Expression for Structs**: C# 10.0 extends the use of the `with` expression to struct types, enabling you to easily create modified copies of struct instances.

```csharp
public struct Rectangle
{
    public Point TopLeft { get; init; }
    public Point BottomRight { get; init; }

    public Rectangle Move(int dx, int dy)
    {
        // Using 'with' expression to create a new Rectangle instance
        // with modified TopLeft and BottomRight points
        return this with
        {
            TopLeft = new Point { X = TopLeft.X + dx, Y = TopLeft.Y + dy },
            BottomRight = new Point { X = BottomRight.X + dx, Y = BottomRight.Y + dy }
        };
    }
}
```

- This example demonstrates how to use the `with` expression to create a new `Rectangle` instance based on an existing one, with adjusted `TopLeft` and `BottomRight` positions.

### Interpolated string handlers

The new or enhanced feature of "Interpolated String Handlers" in C# 10.0 is designed to make the construction of strings from interpolated string expressions more efficient and versatile. In C# 10.0, you can create a custom type that handles the construction of the string result from an interpolated string expression. This is particularly useful for reducing unnecessary allocations and improving performance when constructing strings, as the feature allows for more control over how the interpolated string is processed and formatted.

One of the key benefits of this feature is its use in APIs that frequently work with string interpolation, enabling a more efficient handling of such operations. By implementing a custom interpolated string handler, developers can optimize the way strings are built, making the process more efficient by avoiding unnecessary temporary allocations and directly constructing the final string result.

This enhancement is part of a broader effort to improve the C# language's performance and flexibility, especially in scenarios where string manipulation and construction are critical. The feature is leveraged by the .NET libraries in various APIs, demonstrating its utility and effectiveness in real-world applications. Overall, "Interpolated String Handlers" in C# 10.0 offer a powerful mechanism for optimizing string interpolation, making it a valuable feature for developers looking to improve the performance and maintainability of their C# applications.

- **Basic Usage of Interpolated String Handlers**

```csharp
// Define a custom interpolated string handler
[InterpolatedStringHandler]
public ref struct LogMessageHandler
{
    StringBuilder builder;
    // Constructor initializing with expected sizes
    public LogMessageHandler(int literalLength, int formattedCount)
    {
        builder = new StringBuilder(literalLength);
    }
    // Append literal strings
    public void AppendLiteral(string s)
    {
        builder.Append(s);
    }
    // Append formatted objects
    public void AppendFormatted<T>(T t)
    {
        builder.Append(t?.ToString());
    }
}

// Example usage of the custom handler
var name = "John";
var age = 30;
var logMessage = $"User: {name}, Age: {age}"; // This will use the custom LogMessageHandler
Console.WriteLine(logMessage);
```

- **Conditional Formatting with Interpolated String Handlers**

```csharp
// Enhanced version with conditional formatting based on type
public void AppendFormatted<T>(T t)
{
    if (typeof(T) == typeof(int))
    {
        // Special formatting for integers
        builder.Append($"Integer: {t}");
    }
    else
    {
        // Default formatting for other types
        builder.Append(t?.ToString());
    }
}

// Usage example with enhanced formatting
var userID = 102;
var username = "Alice";
var logMessageEnhanced = $"UserID: {userID}, Username: {username}";
Console.WriteLine(logMessageEnhanced);
```

- **Using Interpolated String Handlers with Methods**

```csharp
// A method that accepts an interpolated string with custom handler
void LogDebugMessage([InterpolatedStringHandlerArgument("")] LogMessageHandler message)
{
    // Assume this logs the message to some debug output
    Console.WriteLine(message.GetFormattedText()); // Method to obtain the final string
}

// Call the method with an interpolated string
LogDebugMessage($"Action: User login, Username: {username}, Time: {DateTime.Now}");
```

### Global using directives

In C# 10.0, the "Global using directives" feature was introduced to streamline how developers manage namespace imports across their projects. This feature allows you to add the `global` modifier to any `using` directive, making it applicable to all source files in your project. This enhancement is particularly useful for reducing redundancy and clutter at the top of each file, where common namespaces are typically imported.

For instance, if you find yourself repeatedly importing the same namespaces across various files, such as `System`, `System.Collections.Generic`, or `System.Linq`, you can now declare these imports once using the `global` keyword, and they will automatically apply to every file within the scope of your project. This change leads to cleaner code and less repetition, simplifying project maintenance and readability.

- **Simplifying Project-Wide Namespaces with Global Using Directives**

  Global using directives allow for project-wide inclusion of common namespaces, making them available in all source files without individual declaration.

  - **Declaring Global Usings in a Separate File**

  ```csharp
  // globalusings.cs
  // This file contains global using directives that are applied across all source files in the project.
  global using System;
  global using System.Collections.Generic;
  global using System.Linq;
  ```

  _Comment:_ By placing global using directives in this file, namespaces like `System`, `System.Collections.Generic`, and `System.Linq` are automatically made available across all source files, eliminating the need for redundant declarations.

  - **Utilizing Global Usings in Your Code**

  ```csharp
  // Program.cs
  // With global using directives declared, you can directly use types from those namespaces without explicitly declaring them in this file.

  var numbers = new List<int> { 1, 2, 3 };
  var filteredNumbers = numbers.Where(n => n > 1).ToList();

  foreach (var number in filteredNumbers)
  {
      Console.WriteLine(number);
  }
  ```

  _Comment:_ This example demonstrates the convenience of global using directives, allowing the direct use of `List<int>` and LINQ methods without specifying their namespaces explicitly.

- **Combining Global and Local Using Directives**

  Global using directives complement rather than replace local using directives, enabling an efficient combination of both where necessary.

  - **Combining Global and Local Usings**

  ```csharp
  // SpecificFile.cs
  // For file-specific namespaces, local using directives can still be used alongside global ones.
  using System.IO;

  var filePath = "example.txt";
  if (File.Exists(filePath))
  {
      var content = File.ReadAllText(filePath);
      Console.WriteLine(content);
  }
  ```

  _Comment:_ In scenarios requiring specific namespaces less common across the project, local using directives (e.g., `System.IO` for file operations) can be utilized alongside global ones for flexibility and clarity.

### File-scoped namespace declaration

The new feature of "File-scoped namespace declaration" in C# 10.0 allows you to declare a namespace for the entire file with a simpler syntax. This feature helps to save both horizontal and vertical space in your code. Instead of wrapping your code within curly braces `{}` to define a namespace scope, you can now declare the namespace at the top of your file followed by a semicolon. This means that all declarations that follow in the file are considered part of the declared namespace, eliminating the need for additional indentation and braces. This feature is particularly useful for files that contain code in a single namespace, simplifying the file's structure and improving readability.

- **Basic File-Scoped Namespace Declaration**:
- **Old Syntax (Prior to C# 10.0)**:

```csharp
// Traditional namespace declaration requiring curly braces and additional indentation
namespace MyApplication.Utilities
{
    // Class declaration inside the namespace
    public class MathUtilities
    {
        // Method to add two numbers
        public int Add(int a, int b) => a + b;
    }
}
```

- **New Syntax (C# 10.0)**:

```csharp
// File-scoped namespace declaration without curly braces reduces nesting
namespace MyApplication.Utilities;

// Class declaration directly follows the namespace declaration
public class MathUtilities
{
    // Method to add two numbers, demonstrating cleaner code with less indentation
    public int Add(int a, int b) => a + b;
}
```

_This example shows how the new file-scoped namespace feature in C# 10.0 simplifies the syntax by removing the need for curly braces and reducing code indentation._

- **Combining Global Usings with File-Scoped Namespaces**: GlobalUsings.cs (Global Using Declarations):

```csharp
// Global using directives apply to the entire project
global using System;
global using System.Collections.Generic;
```

- **Calculator.cs (Using File-Scoped Namespace)**:

```csharp
// File-scoped namespace declaration for the Calculator class
namespace MyApplication.Calculations;

// Calculator class uses types from global using directives
public class Calculator
{
    // Method to sum an enumerable of numbers
    public int Sum(IEnumerable<int> numbers) => numbers.Sum();
}
```

_In this example, the combination of global using directives and file-scoped namespaces showcases how C# 10.0 features can be used together to write more concise and readable code._

### Extended property patterns

The new feature of "Extended property patterns" in C# 10.0 simplifies how developers can match and destructure data in patterns, particularly for nested properties. Previously, to match a nested property within an object, you had to nest the patterns accordingly. This could lead to more verbose and harder-to-read code, especially with deeply nested objects.

In C# 10.0, extended property patterns allow you to reference nested properties directly in a more streamlined and readable manner. For instance, instead of writing nested patterns like `{ Prop1: { Prop2: pattern } }`, you can now write `{ Prop1.Prop2: pattern }`. This enhancement makes the code cleaner and improves readability by reducing the nesting required for matching nested properties.

This feature is particularly useful when working with complex data structures, making it easier to focus on the specific data you're interested in within a pattern match. It also aligns with the goal of making C# more expressive and efficient for data manipulation tasks.

- **Example Code**:

```csharp
// Define a nested class structure with properties
class Person
{
    public string Name { get; init; }
    public Address HomeAddress { get; init; }
}

class Address
{
    public string City { get; init; }
    public string Country { get; init; }
}

// Example usage of extended property patterns
void PrintPersonFromCity(Person person)
{
    // Traditional property pattern prior to C# 10.0:
    // if (person is { HomeAddress: { City: "Seattle" } })

    // Using extended property patterns in C# 10.0
    // This checks if the person's home city is Seattle in a more concise manner
    if (person is { HomeAddress.City: "Seattle" })
    {
        Console.WriteLine($"{person.Name} is from Seattle.");
    }
    else
    {
        Console.WriteLine($"{person.Name} is not from Seattle.");
    }
}

// Example of creating a Person object and using the PrintPersonFromCity method
void Example()
{
    var person = new Person
    {
        Name = "John Doe",
        HomeAddress = new Address { City = "Seattle", Country = "USA" }
    };

    PrintPersonFromCity(person); // Output: John Doe is from Seattle.
}

// Call the example method to demonstrate the functionality
Example();
```

1. **Class Definitions**: Two classes, `Person` and `Address`, are defined to create a nested structure where a `Person` has an `Address`.

2. **Extended Property Patterns**: The `PrintPersonFromCity` method demonstrates the use of extended property patterns introduced in C# 10.0. This feature allows us to directly access nested properties (`HomeAddress.City`) in a pattern matching expression, making the code more concise compared to the traditional approach.

3. **Example Usage**: An example person named "John Doe" from "Seattle" is created, and the `PrintPersonFromCity` method is called to demonstrate the use of extended property patterns. The output verifies that the person is indeed from Seattle, showcasing the pattern matching capability.

4. **Comments**: Throughout the code, comments are added to explain the purpose of each part and to highlight the differences between traditional property patterns and extended property patterns introduced in C# 10.0.

### Improvements on lambda expressions

In C# 10.0, one notable improvement is on lambda expressions. These enhancements make working with lambda expressions more intuitive and powerful. Here's a concise explanation tailored for a B2 English level:

"C# 10.0 made working with 'lambda expressions' even better. Now, when you write small functions right in your code (these are called lambda expressions), C# can understand what type of function you mean without you having to specify it. It's like if you're cooking and say 'hand me the big spoon,' and someone knows exactly which spoon you need without looking. Also, you can now be more specific about what your little function returns, which is like saying 'I want a big, wooden spoon' to be extra clear. Plus, you can attach special labels to these functions, making them do special things, just like adding a spice to a dish for extra flavor."

This explanation avoids technical jargon and simplifies the concept to make it understandable at a B2 English proficiency level, without the need for code samples.

- **Natural Type Inference**: C# 10 introduced improvements that allow lambda expressions to have a natural type, where the compiler can infer a delegate type from the lambda expression itself. This makes it easier to use lambda expressions without having to explicitly declare a delegate.

```csharp
// Before C# 10, you might have to specify the delegate type explicitly
Func<int, int> square = x => x * x;

// With C# 10, the compiler can infer the delegate type
var square = (int x) => x * x;
// This lambda expression is of type Func<int, int> due to natural type inference
```

- **Lambda Expressions with Attributes**: C# 10 allows attributes to be applied directly to lambda expressions. This can be useful for providing additional information to the compiler or for use with frameworks that support it.

```csharp
// Applying an attribute to a lambda expression in C# 10
var lambdaWithAttribute = [System.Diagnostics.CodeAnalysis.NotNull] () => { };
// This can be useful for code analysis tools or other frameworks that utilize attributes
```

- **Explicit Return Types in Lambda Expressions**: In situations where the compiler cannot infer the return type of a lambda expression, C# 10 allows developers to explicitly specify it. This is particularly useful when the lambda expression involves conditional logic that returns different types.

```csharp
// Using explicit return types in lambda expressions
var mixedReturn = (bool condition) => condition ? 1 : "one";
// In C# 10, you can specify the return type explicitly if it cannot be inferred
var mixedReturn = object (bool condition) => condition ? 1 : "one";
// This lambda returns an object, which can either be an int or a string
```

### Allow const interpolated strings

In C# 10.0, the feature of "Allowing const interpolated strings" was introduced to enable the use of interpolated strings within `const` declarations. This enhancement allows developers to define constant strings using interpolated expressions, providing more flexibility and expressiveness when working with string constants in C#.

- **Basic Usage**

```csharp
// Using const interpolated strings for concise and readable code
// Before C# 10.0, you had to concatenate const strings without interpolation
const string greeting = "Hello";
const string target = "World";
const string messageBeforeCSharp10 = greeting + ", " + target + "!";

// With C# 10.0, you can use string interpolation for const strings, making the code more readable
// Note: All placeholders in the interpolated string must themselves be constant expressions.
const string messageInCSharp10 = $"{greeting}, {target}!";

Console.WriteLine(messageBeforeCSharp10); // Output: Hello, World!
Console.WriteLine(messageInCSharp10);     // Output: Hello, World!
```

- **Building URLs**

```csharp
// Building a constant URL using interpolated strings in C# 10.0
// Useful for cases where base URL and endpoints are constant and need to be combined
const string baseUrl = "https://api.example.com";
const string resource = "users";
const string apiUrl = $"{baseUrl}/{resource}";

Console.WriteLine(apiUrl); // Output: https://api.example.com/users
```

### Record types can seal ToString()

In C# 10.0, one of the new features is the ability to seal the `ToString()` method in record types. This means you can prevent derived record types from overriding the `ToString()` method by adding the `sealed` modifier when overriding this method in a base record type. This ensures that all derived record types use the `ToString()` method defined in the common base record type, making the `ToString()` representation consistent across an inheritance hierarchy of records. This feature is particularly useful for controlling how record types are represented as strings, especially when you have a specific format you want to maintain without allowing further customization in derived types.

- **Basic Usage of Sealing ToString in a Record Type**

```csharp
// Define a base record type with a sealed ToString method.
// This prevents any derived record type from overriding the ToString method.
public record Person(string FirstName, string LastName)
{
    // Seal the ToString method to ensure consistent string representation
    // across all derived types.
    public sealed override string ToString()
    {
        return $"{FirstName} {LastName}";
    }
}

// Define a derived record type from Person.
public record Employee(string FirstName, string LastName, string Position) : Person(FirstName, LastName)
{
    // Attempting to override ToString in this record would result in a compilation error
    // because ToString is sealed in the base record Person.
}

// Usage of the record types.
public class Program
{
    public static void Main(string[] args)
    {
        var person = new Person("John", "Doe");
        var employee = new Employee("Jane", "Doe", "Developer");

        // Both record types use the ToString method defined and sealed in the Person record.
        Console.WriteLine(person);  // Output: John Doe
        Console.WriteLine(employee);  // Output: Jane Doe
    }
}
```

- **Demonstrating Sealed ToString with a Custom Base Record**

```csharp
// Define a custom base record with additional properties and a sealed ToString method.
public record CustomBaseRecord(string Id, DateTime CreatedAt)
{
    // Sealing the ToString ensures all derived records utilize this base representation.
    public sealed override string ToString()
    {
        return $"Id: {Id}, CreatedAt: {CreatedAt}";
    }
}

// Define a derived record with specific properties, extending CustomBaseRecord.
public record DetailedRecord(string Id, DateTime CreatedAt, string Details) : CustomBaseRecord(Id, CreatedAt)
{
    // There is no need (and no ability) to override ToString here,
    // as it's been sealed in the CustomBaseRecord.
}

// Demonstrate usage of the sealed ToString in derived records.
public class Demo
{
    public static void Main()
    {
        var detailedRecord = new DetailedRecord("001", DateTime.Now, "Extra Details");

        // Uses the ToString implementation from CustomBaseRecord, which includes Id and CreatedAt.
        Console.WriteLine(detailedRecord);
    }
}
```

### Improved definite assignment

The "Improved definite assignment" feature in C# 10.0 enhances the accuracy and reduces the likelihood of false positive warnings related to definite assignment and null-state analysis. Prior to C# 10.0, the compiler could incorrectly warn about uninitialized variables or null references in scenarios involving boolean comparisons, variable access within conditional statements, and null-coalescing expressions. With this improvement, such warnings are more precise, reducing unnecessary alerts during code development and analysis. This feature streamlines the process of determining variable initialization and nullability, making it easier to write safer and more reliable code without the distraction of unwarranted compiler warnings.

```csharp
// Example 1: Improved accuracy with boolean logic
bool IsValid(object obj, out string message)
{
    message = string.Empty;
    // Prior to C# 10.0, the compiler couldn't definitely determine that 'message' would always be assigned before usage
    // Now, with improved definite assignment, it understands complex boolean logic paths better
    if (obj == null || !TryValidate(obj, out message))
    {
        message = "Invalid object."; // C# 10.0 correctly identifies 'message' is always assigned
        return false;
    }
    return true;
}

// Example 2: Using pattern matching with null checking
public void ProcessData(Data data)
{
    // In previous versions, even with a null check, the compiler might warn about potential use of uninitialized variables
    // C# 10.0's improvements recognize when variables are definitely assigned after null checks and pattern matching
    if (data is { Length: > 0 })
    {
        Console.WriteLine($"Processing {data.Length} items."); // No warnings about 'data' being possibly null
    }
    else
    {
        Console.WriteLine("Data is null or empty.");
    }
}

// Example 3: Leveraging definite assignment with property patterns
public void LogPerson(Person person)
{
    // C# 10.0 allows more sophisticated use of property patterns without confusing definite assignment analysis
    if (person is { FirstName: not null, LastName: not null })
    {
        Console.WriteLine($"Logging person: {person.FirstName} {person.LastName}");
        // The compiler understands both 'FirstName' and 'LastName' are definitely assigned if this block is reached
    }
    else
    {
        Console.WriteLine("Person or person's name is incomplete.");
    }
}

// Note: These examples demonstrate how C# 10.0's improved definite assignment allows writing more expressive and safe code without compromising on clarity or generating unnecessary compiler warnings.
```

### Allow both assignment and declaration in the same deconstruction

Simplifies how variables can be handled during deconstruction. In previous versions of C#, when you deconstructed a tuple or an object, you had to either declare all variables anew or assign values to existing variables. C# 10.0 removes this limitation, allowing a mix of declaring new variables and assigning values to existing ones in the same deconstruction operation. This change makes the code more concise and flexible, enabling you to efficiently initialize and assign values without needing separate lines or additional code for handling variables. The ability to combine declaration and assignment in deconstruction enhances the readability and expressiveness of C# code, especially in scenarios where multiple variables are involved.

- **Basic Usage**: Before C# 10.0, when deconstructing a tuple or an object with deconstruction methods, you had to either declare new variables for all the values or assign them to existing variables. Mixing both actions was not possible. C# 10.0 introduces the flexibility to combine both declaration and assignment in the same deconstruction operation.

```csharp
// Assuming we have a tuple representing a point with X and Y coordinates
var point = (X: 5, Y: 10);

// Before C# 10.0, you had to do this:
int existingX = 1;
int existingY = 2;
(existingX, existingY) = point; // Assigning to existing variables

// Or declare new variables
(var newX, var newY) = point;

// With C# 10.0, you can mix both approaches:
int mixX = 3; // Existing variable
// Mixing assignment to existing variable and declaration of a new variable
(mixX, int mixY) = point;

Console.WriteLine($"Existing X after assignment: {existingX}, Existing Y after assignment: {existingY}");
Console.WriteLine($"New X: {newX}, New Y: {newY}");
Console.WriteLine($"Mixed X: {mixX}, Mixed Y after declaration and assignment: {mixY}");
```

- **Deconstructing Custom Objects**: C# 10.0's enhanced deconstruction is particularly useful when working with custom objects that support deconstruction. This feature streamlines the process of extracting multiple values from an object.

```csharp
// A custom class with deconstruction method
public class Rectangle
{
    public int Length { get; }
    public int Width { get; }

    public Rectangle(int length, int width)
    {
        Length = length;
        Width = width;
    }

    // Deconstruction method
    public void Deconstruct(out int length, out int width)
    {
        length = Length;
        width = Width;
    }
}

// Usage
var myRectangle = new Rectangle(20, 10);

int existingLength = 5;
// Mixing assignment to an existing variable and declaring a new one
(existingLength, int newWidth) = myRectangle;

Console.WriteLine($"Existing Length after assignment: {existingLength}");
Console.WriteLine($"Newly declared Width: {newWidth}");
```

### Allow AsyncMethodBuilder attribute on methods

The feature "Allow AsyncMethodBuilder attribute on methods" introduced in C# 10.0 allows for specifying a custom asynchronous method builder for individual async methods. This capability enables advanced performance tuning scenarios where a specific method can benefit from a custom builder, differing from the default behavior for task-like types. By attributing a method with a specific AsyncMethodBuilder, developers can tailor the execution and state management of asynchronous operations, offering more control over asynchronous code execution patterns and optimizing performance for specific tasks or computational models. This enhancement adds to the flexibility and power of C#’s already robust asynchronous programming model, allowing for more fine-tuned and efficient async operations.

```csharp
using System;
using System.Runtime.CompilerServices;
using System.Threading.Tasks;

// Define a custom async method builder.
public class CustomAsyncMethodBuilder
{
    // Method to initiate the builder. Required by async methods.
    public static CustomAsyncMethodBuilder Create() => new CustomAsyncMethodBuilder();

    // Necessary to build the task. Here, a simple Task is returned for the sake of example.
    public Task Task => Task.CompletedTask;

    // Start the async operation. This is simplified for demonstration.
    public void Start<TStateMachine>(ref TStateMachine stateMachine) where TStateMachine : IAsyncStateMachine
    {
        // Logic to start the state machine.
        stateMachine.MoveNext();
    }

    // Set the result of the operation. This example assumes a Task (non-generic).
    public void SetResult()
    {
        // Logic to set the result of the async operation.
    }

    // Mark the task as failed.
    public void SetException(Exception e)
    {
        // Logic to handle exceptions.
    }

    // Other necessary members for the builder...
}

// Apply the AsyncMethodBuilder attribute to a method.
// This specifies that the CustomAsyncMethodBuilder should be used for this async method.
[AsyncMethodBuilder(typeof(CustomAsyncMethodBuilder))]
public static async Task ExampleMethodAsync()
{
    // The body of the async method. The custom async method builder is used for this method.
    await Task.Delay(1000); // Example async operation.
}

// A sample usage
class Program
{
    static async Task Main(string[] args)
    {
        // Call the method with the custom async method builder.
        await ExampleMethodAsync();
    }
}
```

**Key Points:**

- **Custom Async Method Builder**: The `CustomAsyncMethodBuilder` class is a simplified example of a custom async method builder. It includes the necessary members to satisfy the async pattern requirements, such as `Create()`, `Start<TStateMachine>()`, `SetResult()`, and `SetException()` methods.

- **Applying AsyncMethodBuilder**: The `AsyncMethodBuilder` attribute is applied to `ExampleMethodAsync()`, directing the compiler to use `CustomAsyncMethodBuilder` for this method. This customization allows for specific optimization or behavior that the standard async builders (e.g., `AsyncTaskMethodBuilder`) might not provide.

- **Usage**: The async method `ExampleMethodAsync` is then called in the `Main` method. Despite the custom builder, the call site remains unchanged, demonstrating the seamless integration of custom async method builders into existing code.

### CallerArgumentExpression attribute

The "CallerArgumentExpression" attribute in C# 10.0 provides a powerful way to capture and utilize the expressions passed as arguments to methods. This feature is especially useful for creating more detailed and informative diagnostic messages, as it allows you to include the actual code that a developer writes in the call site within exception messages or logging. For example, when performing argument validation, instead of simply notifying that a value is incorrect, you can provide the exact expression that resulted in the error, making it easier to identify and fix issues. This enhancement to C# improves code clarity and debugging efficiency by leveraging the expressions passed to methods directly in your diagnostic logic.

- **Basic Usage of CallerArgumentExpression**

```csharp
using System;
using System.Runtime.CompilerServices;

// A method that uses CallerArgumentExpression to capture and log the caller's argument expression
public static class Logger
{
    public static void LogIfFalse(bool condition, [CallerArgumentExpression("condition")] string? conditionExpression = null)
    {
        if (!condition)
        {
            Console.WriteLine($"Check failed: {conditionExpression}");
        }
    }
}

class Program
{
    static void Main()
    {
        int x = 5;
        // Using the Logger to log if a condition is false. The condition expression "x > 10" is captured automatically.
        Logger.LogIfFalse(x > 10);
    }
}
```

- This example demonstrates a basic usage of the `CallerArgumentExpression` attribute to capture and log the expression passed as an argument to a method if the condition is false. This feature is useful for debugging and logging purposes, making it easier to understand the context of the failure.

- **Using CallerArgumentExpression for Argument Validation**

```csharp
using System;
using System.Runtime.CompilerServices;

// A method that uses CallerArgumentExpression for argument validation, providing detailed messages on argument validation failures
public static class ArgumentValidator
{
    public static void NotNull(object? argument, [CallerArgumentExpression("argument")] string? argumentExpression = null)
    {
        if (argument == null)
        {
            throw new ArgumentNullException(argumentExpression, $"Argument cannot be null: {argumentExpression}");
        }
    }
}

class MyClass
{
    public MyClass(object? myParameter)
    {
        // Using the validator to check if myParameter is null. If null, throws ArgumentNullException with detailed message.
        ArgumentValidator.NotNull(myParameter);
    }
}
```

- In this example, the `CallerArgumentExpression` attribute is used to implement a simple argument validation method that throws an `ArgumentNullException` with a detailed message if the argument is null. This enhances the clarity of error messages related to argument validation by including the expression of the argument that caused the issue.

### Enhanced #line pragma

Designed to provide a more fine-grained output for domain-specific languages, such as Razor. This enhancement allows debuggers to more accurately highlight source code during debugging sessions, thereby improving the overall debugging experience. You might not directly use this new format, but its effects will significantly benefit the development process, especially when working with languages like Razor to generate more precise and informative debug information​. The enhanced `#line` pragma in C# 10.0 offers a more detailed and accurate way to control the source code line mappings for debugging and error reporting purposes. By specifying additional information such as the file name, line number, and column number, developers can precisely define the source location for specific code segments. This feature is particularly useful for scenarios where code generation or transformation tools are involved, ensuring that debuggers and diagnostic tools can accurately map the generated code back to the original source, improving the debugging experience and error reporting accuracy.

- **Basic Usage of Enhanced #line pragma**: In C# 10, the enhanced `#line` pragma can be used to control line numbers and file paths in compiler-generated output. This is particularly useful when generating code from templates or other sources, as it allows for more accurate error and warning messages that point back to the correct location in the original source files.

```csharp
// Before: Traditional #line directive usage
#line 200 "MyGeneratedFile.cs"
Console.WriteLine("Hello, World!");
#line default

// C# 10: Enhanced #line pragma for improved debugging experience
// Assume this code is generated from a template. By using the enhanced #line pragma,
// errors in the console application will be reported in the original template file,
// making it easier to track down and correct issues.

#line (FileName="OriginalTemplateFile.txt", Line=10)
Console.WriteLine("Hello, World from template!");
#line default
// After this point, line numbers and file paths are again automatically managed by the compiler.
```

- **Using Enhanced #line pragma in Auto-Generated Razor Files**: Razor pages in ASP.NET Core applications often generate C# code behind the scenes. The enhanced `#line` pragma in C# 10 helps map errors in generated code back to the original Razor page, greatly improving the developer debugging experience.

```csharp
// Original Razor file: Index.cshtml
@{
    // Razor syntax is used here
}

// Auto-generated C# code from Razor might look like this:
#line (FileName="Index.cshtml", Line=2)
// The code below is generated from the Razor syntax in Index.cshtml
public class IndexModel : PageModel
{
    public void OnGet()
    {
        // Some auto-generated code based on Razor page
    }
}
#line default
// Debugging errors in the OnGet method will point back to the Index.cshtml file,
// thanks to the enhanced #line pragma.
```

### Warning wave 6

"Warning wave 6" in C# 10.0 is aimed at improving code quality by providing developers with more detailed compiler warnings. This feature enhances the compiler's ability to analyze code and issue warnings that can help developers identify potential issues or inefficiencies in their codebase. With Warning wave 6, the C# compiler has been upgraded to include additional rules and checks that can detect more subtle code problems, encouraging best coding practices and helping maintain high code standards. This feature does not alter the language syntax or introduce new language constructs; instead, it leverages the existing compiler infrastructure to offer better feedback to developers, making it easier to produce robust, error-free code.

- **Correct Use of Nullable Reference Types**

```csharp
// Enable nullable context to help catch null reference errors at compile time
#nullable enable

class Program
{
    static void Main(string[] args)
    {
        // Warning: Possible null reference assignment.
        // Fix: Ensure 'message' is not null before assignment
        string? message = GetMessage();
        if (message == null)
        {
            message = "Default message"; // Corrected: Assign a non-null value if 'message' is null
        }

        Console.WriteLine(message); // Safe to access 'message' as it's guaranteed to be non-null here
    }

    static string? GetMessage()
    {
        return DateTime.Now.Hour < 12 ? "Good morning" : null; // Intentionally returns null in some cases
    }
}
```

- **Initialization of Properties**

```csharp
// C# 10 allows init-only properties, which can be set at the time of object creation
class User
{
    public string Name { get; init; } // Init-only property

    // Warning: Non-nullable property 'Name' must be initialized
    // Fix: Initialize 'Name' through the constructor
    public User(string name)
    {
        Name = name ?? throw new ArgumentNullException(nameof(name)); // Ensure 'Name' is not set to null
    }
}

// Usage
var user = new User("John Doe"); // Corrected: 'Name' is initialized through the constructor
```

- **Using Declarations and Pattern Matching**

```csharp
// Utilize pattern matching to simplify and clarify code, avoiding potential issues
void ProcessOrder(Order? order)
{
    // Warning: The expression is always 'true' or always 'false'
    // Fix: Use pattern matching to clearly express the intent
    if (order is not null) // Corrected: Clear and concise check for 'order' not being null
    {
        Console.WriteLine($"Processing order: {order.Id}");
    }
    else
    {
        Console.WriteLine("Order is null.");
    }
}

class Order
{
    public int Id { get; set; }
}
```

- Each example addresses a different aspect of the warnings that might be part of Warning Wave 6 in C# 10.0:
  - The first example shows how to work with nullable reference types to avoid null reference exceptions.
  - The second example demonstrates the proper initialization of properties to ensure that non-nullable properties are always in a valid state.
  - The third example illustrates the use of pattern matching to make null checks more concise and clear, aligning with best practices and reducing the chance of errors.

## Conclusion

The introduction of C# 10.0 brings several significant enhancements to the language, marking another step forward in its evolution. From record structs, global using directives, and file-scoped namespace declarations to extended property patterns, improved lambda expressions, and constant interpolated strings, each of these features offers developers more tools to write cleaner, more efficient, and more expressive code.

The addition of these features demonstrates a continued commitment to language improvement, responding to community feedback and evolving programming practices. As developers explore and adopt these new capabilities, they will likely find their C# projects becoming more concise and their coding workflows more streamlined.

In conclusion, C# 10.0's new features reflect both an adherence to the language's core principles of simplicity, expressiveness, and performance, and a forward-looking approach to modern software development challenges. As the .NET ecosystem continues to grow and diversify, these improvements ensure that C# remains a top choice for developers working across a wide range of applications and industries.

[Previous: C# 9.0 New Features Overview](./csharp-9.0.md) | [Back to main page](../../README.md) | [Next: C# 11.0 New Features Overview](./csharp-11.0.md)
