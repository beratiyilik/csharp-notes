# C# 6.0 New Features Overview

## Table of Contents

- [C# 6.0 New Features Overview](#c-60-new-features-overview)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [New Features](#new-features)
    - [Auto-Property Enhancements](#auto-property-enhancements)
    - [String Interpolation](#string-interpolation)
    - [`nameof` Expressions](#nameof-expressions)
    - [Null-conditional Operators](#null-conditional-operators)
    - [Exception Filters](#exception-filters)
    - [Expression-bodied Members](#expression-bodied-members)
    - [Await in Catch and Finally Blocks](#await-in-catch-and-finally-blocks)
    - [Index Initializers](#index-initializers)
  - [Conclusion](#conclusion)

## Introduction

This guide introduces the new features in C# 6.0, designed to make coding simpler and more efficient. These enhancements include Auto-Property Initializers for easier property setup, String Interpolation for more readable strings, `nameof` Expressions to avoid errors with magic strings, and Null-conditional Operators for safer code. Also featured are Exception Filters for more precise error handling, Expression-bodied Members to streamline class members, Await in Catch and Finally Blocks for better asynchronous programming, and Index Initializers for improved object initialization. Each feature adds valuable tools for developers, making C# code more concise, readable, and maintainable.

## New Features

### Auto-Property Enhancements

Auto-property initializers in C# 6.0 streamline .NET property setup by allowing direct initial value assignments within property declarations, eliminating the need for explicit backing fields and constructors. They support both immutable (read-only) and mutable (read/write) properties, with read-only ones getting an automatic read-only backing field to ensure immutability. Developers can use expressions for initialization, including conditionals for dynamic values, or static methods for more complex logic, improving code brevity and readability.

- **Auto-Property Initializers**: Enable properties to be initialized directly at their declaration.
- **Getter-only Auto-Properties**: Allow for read-only properties to be declared more succinctly.
- **Basic Usage**: This example shows the basic usage of auto-property initializers for both read-only and read/write properties. `Balance` is immutable after initialization, whereas `Owner` can be changed.

  ```csharp
  public class Account
  {
      public decimal Balance { get; } = 100.0m; // Auto-property initializer for a read-only property
      public string Owner { get; set; } = "John Doe"; // Auto-property initializer for a read/write property
  }
  ```

- **Conditional Initialization**: Here, the `ConnectionString` property is initialized based on a conditional check. This example demonstrates the use of expressions in property initializers for dynamic default values.

  ```csharp
  public class Configuration
  {
      public string ConnectionString { get; } =
          string.IsNullOrWhiteSpace(
              (string)Properties.Settings.Default["connectionString"]) ?
              "<none>" : (string)Properties.Settings.Default["connectionString"];
  }
  ```

- **Using Static Methods for Complex Initializations**: In this example, the `Default` property is initialized using a static method `CreateDefaultLogger()`, which allows for more complex initialization logic.

  ```csharp
  public class Logger
  {
      public static Logger Default { get; } = CreateDefaultLogger();

      private static Logger CreateDefaultLogger()
      {
          return new Logger("DefaultLogger");
      }

      public string Name { get; }

      private Logger(string name)
      {
          Name = name;
      }
  }
  ```

### String Interpolation

String interpolation in C# allows embedding expressions into strings by prefixing the string with `$` and enclosing expressions in `{}`. It supports format strings and alignment, uses current culture for formatting by default, and supports escaping braces and verbatim strings. To create culture-specific or invariant culture result strings, use `String.Create` with a culture or `FormattableString.Invariant`. C# string interpolation is a powerful feature that enhances readability and maintainability by allowing inline expressions, format specifiers, and culture-specific formatting within string literals.

- **Basic Usage**: This example demonstrates the basic usage of string interpolation, where expressions are enclosed in `{}` within the string.

  ```csharp
  string name = "Alice";
  int age = 30;
  Console.WriteLine($"Name: {name}, Age: {age}");
  ```

- **Format Strings and Alignment**: In this example, the `price` variable is formatted as currency with two decimal places, and the output is right-aligned within a field of width 10.

  ```csharp
    double price = 123.45;
    Console.WriteLine($"Price: {price:C2}"); // Currency formatting
    Console.WriteLine($"Price: {price,10:C2}"); // Right-aligned currency formatting

    DateTime now = DateTime.Now;
    Console.WriteLine($"Current time: {now:HH:mm:ss}");
    // Output: Current time: 14:55:02 (assuming it's 2:55 PM)
  ```

### `nameof` Expressions

The `nameof` expression in C# 6.0 is a tool that gives you the name of a variable, type, or member as a string. It helps avoid errors in code when referring to these names. For example, instead of manually writing the name of a variable as a string, using `nameof` will automatically update the name in your string if you rename the variable. This makes your code more reliable and easier to maintain.

- **Avoiding Magic Strings for Parameter Names**: In this example, the `nameof` operator is used to obtain the parameter name `input` as a string. This approach eliminates the need for hard-coded parameter names and ensures that the exception message remains consistent with the parameter name.

  ```csharp
  public void ValidateInput(string input)
  {
      if (input == null)
      {
          // throw new ArgumentNullException("input"); // Magic string
          throw new ArgumentNullException(nameof(input));
      }
  }
  ```

- **PropertyChanged Events**: The `nameof` operator is used to pass the property name to the `OnPropertyChanged` method, ensuring that the event is raised with the correct property name. This approach avoids hard-coded property names and reduces the risk of errors when refactoring code.

  ```csharp
  public event PropertyChangedEventHandler PropertyChanged;

  protected virtual void OnPropertyChanged(string propertyName)
  {
      PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
  }

  public string Name
  {
      get { return _name; }
      set
      {
          if (_name != value)
          {
              _name = value;
              OnPropertyChanged(nameof(Name));
          }
      }
  }
  ```

- **Retrieving Class, Method, and Attribute Names**: The example below demonstrates the use of `nameof` to obtain the class name, method name, and attribute name as strings. This approach enhances code readability and maintainability by eliminating hard-coded names.

  ```csharp
  namespace CSharp6.Demo
  {
      [TestClass]
      public class NameofDemo
      {
          [TestMethod]
          public void TestMethod()
          {
              // Class name
              Console.WriteLine(nameof(NameofDemo)); // Outputs: "NameofDemo"

              // Method name
              Console.WriteLine(nameof(TestMethod)); // Outputs: "TestMethod"

              // Attribute name (requires full name including 'Attribute')
              Console.WriteLine(nameof(TestClassAttribute)); // Outputs: "TestClassAttribute"
          }
      }
  }
  ```

### Null-conditional Operators

The null-conditional operator introduced in C# 6.0 provides a more succinct way to perform null checks, greatly reducing the risk of `NullReferenceException`s by allowing conditional access to members of an object. It allows for short-circuiting method calls or property accesses when the target is null, thereby preventing the common pitfall of attempting to operate on a null reference. This operator comes in two forms: `?.` for members and `?[...]` for indexers, enabling safer access to objects and their elements without the need for explicit null checks.

- **Simplified Null Checks**

  - Before C# 6.0, checking for null would require explicit conditionals:

  ```csharp
  public static string Truncate(string value, int length)
  {
      if (value != null)
      {
          return value.Substring(0, Math.Min(value.Length, length));
      }
      return null;
  }
  ```

  - With the null-conditional operator, this becomes much more concise:

  ```csharp
  public static string Truncate(string value, int length)
  {
      return value?.Substring(0, Math.Min(value.Length, length));
  }
  ```

- **Null-Propagation in Call Chains**: The operator short-circuits the call chain if the target is null. If `value` is null, the entire expression evaluates to null without throwing a `NullReferenceException`.

  ```csharp
  public static string AdjustWidth(string value, int length)
  {
      return value?.Substring(0, Math.Min(value.Length, length)).PadRight(length);
  }
  ```

- **Working with Collections**: For collections, the null-conditional index operator avoids explicit null checks. This method returns the values at the specified indexes if they exist and are not null.

  ```csharp
  public static IEnumerable<T> GetValueTypeItems<T>(IList<T> collection, params int[] indexes) where T : struct
  {
      foreach (int index in indexes)
      {
          T? item = collection?[index];
          if (item != null) yield return (T)item;
      }
  }
  ```

- **Delegate Invocation**: Safely invoking delegates without the need to check for null. This example demonstrates invoking the `OnTemperatureChanged` event handler if it is not null.

  ```csharp
  OnTemperatureChanged?.Invoke(this, value);
  ```

- **Combination with Coalesce Operator**: Using with the coalesce operator to provide default values. This example returns the length of the `value` string or 0 if `value` is null.

  ```csharp
  List<string> linesOfCode = ParseSourceCodeFile("Program.cs");
  return linesOfCode?.Count ?? 0;
  ```

### Exception Filters

C# 6.0 introduces exception filters, allowing conditions in catch blocks, streamlining error handling by catching exceptions only if specific criteria are met. Allow specifying a condition within a catch block, aiding in more precise exception handling.

- **when Usage**
  ```csharp
    try
    {
        var result = Process(-3, 4); // Attempt to process data
        Console.WriteLine($"Processing succeeded: {result}");
    }
    catch (Exception e) when (e is ArgumentException || e is DivideByZeroException)
    {
        // Handle specific exceptions with a condition
        Console.WriteLine($"Processing failed: {e.Message}");
    }
  ```
- Exception filters simplify exception handling by directly embedding conditions into the catch block, promoting cleaner and more readable code. This feature increases C#'s consistency with VB and F#, while caution is advised to avoid abusing it for unintended side-effects.
- **Comprehensive Example**

  ```csharp
  using System;

  namespace ExceptionFiltersDemo
  {
      class Program
      {
          static void Main(string[] args)
          {
              try
              {
                  Console.WriteLine("Enter a number:");
                  var input = Console.ReadLine();
                  var number = int.Parse(input); // Can throw FormatException

                  Console.WriteLine("Enter divisor:");
                  var divisorInput = Console.ReadLine();
                  var divisor = int.Parse(divisorInput); // Can throw FormatException

                  var result = Divide(number, divisor); // Can throw DivideByZeroException
                  Console.WriteLine($"Result of division: {result}");
              }
              catch (Exception ex) when (ex is FormatException || ex is DivideByZeroException)
              {
                  // Handle FormatException or DivideByZeroException with a common strategy
                  Console.WriteLine("There was a problem with your input or the operation performed.");
                  if (ex is DivideByZeroException)
                  {
                      Console.WriteLine("Division by zero is not allowed.");
                  }
                  else
                  {
                      Console.WriteLine("Please make sure you enter a valid number.");
                  }
              }
              catch (Exception ex) when (LogException(ex))
              {
                  // Re-throw the exception if LogException returns false
                  Console.WriteLine("An unexpected error occurred and was logged.");
              }
          }

          static int Divide(int numerator, int denominator)
          {
              if (denominator == 0)
                  throw new DivideByZeroException("Denominator cannot be zero.");

              return numerator / denominator;
          }

          static bool LogException(Exception ex)
          {
              // Log the exception details to a logging system
              Console.WriteLine($"Logging exception: {ex.Message}");
              return false; // Return false to indicate the exception should not be caught here
          }
      }
  }
  ```

### Expression-bodied Members

Expression-bodied members (functions and properties) introduced in C# 6.0 provide a more succinct syntax for defining class members. This feature can be applied to both methods and read-only properties, simplifying the code for cases where the member's implementation is straightforward. Simplify the implementation of members that can be expressed as a single statement.

- **Expression-bodied Functions**: In C# 6.0, expression-bodied functions allow for the definition of a method using a single expression. This is particularly useful for simple methods or overrides, like `ToString()`.

  ```csharp
  public class Pair<T>
  {
      public T First { get; set; }
      public T Second { get; set; }

      // Expression-bodied function example
      public override string ToString() => $"First: {First}, Second: {Second}";
  }
  ```

- **Expression-bodied Properties**: Expression-bodied members can also be applied to read-only properties, allowing the property getter to be defined using an expression.

  ```csharp
  public class FingerPrint
  {
      public DateTime TimeStamp { get; set; }
      public string Process { get; set; }
      public string Config { get; set; }
      public string User { get; set; }

      // Expression-bodied property example
      public string Text => $"{TimeStamp}: {Process} - {Config} ({User})";
  }
  ```

### Await in Catch and Finally Blocks

Await in `catch` and `finally` blocks allows asynchronous operations to be awaited within these blocks.

- **Basic Usage**
  ```csharp
  try
  {
      await SomeMethodAsync();
  }
  catch (Exception ex)
  {
      await LogExceptionAsync(ex);
  }
  finally
  {
      await CleanupResourcesAsync();
  }
  ```

### Index Initializers

C# 6.0 introduces index initializers, a feature that simplifies the initialization of collections, dictionaries, and other indexable objects. This enhancement allows developers to specify key-value pairs directly within object initializers, making the code more concise and readable. Enhance object initializers to allow indexing directly within them.

- **Basic Usage**
  ```csharp
    // conventional dictionary initialization
    var numbers = new Dictionary<int, string>
    {
        { 7, "seven" },
        { 9, "nine" },
        { 13, "thirteen" }
    };
    // index initializer
    var dictionary = new Dictionary<int, string>
    {
      [1] = "One",
      [2] = "Two",
      [3] = "Three"
    };
  ```

## Conclusion

In this guide, we looked at the new features in C# 6.0. These features include making properties easier to write, adding ways to put expressions in strings, using nameof to avoid magic strings, checking for null in a simpler way, filtering exceptions, writing single-line functions and properties, using await in catch and finally blocks, and new ways to initialize indexes. These changes make C# code cleaner, easier to read, and more efficient. With these improvements, developers can write less code to do more things, which makes programming in C# better and more fun. This guide aimed to show you these new features so you can start using them in your projects.

[Previous: C# New Features Overview](./index.md) | [Back to main page](../../README.md) | [Next: C# 7.0 New Features Overview](./csharp-7.0.md)
