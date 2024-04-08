# C# 7.3 New Features Guide

## Table of Contents

- [C# 7.3 New Features Guide](#c-73-new-features-guide)
  - [Table of Contents](#table-of-contents)
  - [Introduction and Overview](#introduction-and-overview)
  - [New Features](#new-features)
    - [Performance Enhancements for Safe Code](#performance-enhancements-for-safe-code)
      - [Enhanced Access to Fixed Fields](#enhanced-access-to-fixed-fields)
      - [Flexible Ref Local Variables](#flexible-ref-local-variables)
      - [Stackalloc Array Initializers](#stackalloc-array-initializers)
      - [Extended Fixed Statement Usability](#extended-fixed-statement-usability)
      - [Expanded Generic Constraints](#expanded-generic-constraints)
    - [Incremental Improvements](#incremental-improvements)
      - [Tuple Equality Operations](#tuple-equality-operations)
      - [Broader Expression Variable Scope](#broader-expression-variable-scope)
      - [Attributes on Auto-Implemented Property Backing Fields](#attributes-on-auto-implemented-property-backing-fields)
      - [Refined Method Resolution Logic](#refined-method-resolution-logic)
  - [Conclusion](#conclusion)

## Introduction and Overview

Welcome to the guide on the new features introduced in C# 7.3! This version of C# brings several enhancements aimed at making your code more efficient, safer, and easier to write. At the heart of these updates is a significant focus on performance enhancements for safe code. For instance, C# 7.3 allows for direct access to fixed fields without needing to pin them, making interactions with unmanaged code much smoother and faster. Additionally, there are new flexible ways to work with `ref` local variables, improvements for stack-allocated arrays, and more versatile `fixed` statements.

Moreover, C# 7.3 expands on generic constraints, providing developers with the ability to specify more detailed type restrictions in their generic code. This release also introduces incremental improvements like tuple equality operations, broader expression variable scope, the ability to apply attributes to auto-implemented property backing fields, and refined method resolution logic. These features not only boost performance but also enhance the readability and maintainability of code.

Whether you are working on high-performance applications, dealing with unmanaged code, or simply aiming for cleaner and more efficient C# programming, this guide will walk you through the exciting new features that C# 7.3 has to offer.

## New Features

### Performance Enhancements for Safe Code

#### Enhanced Access to Fixed Fields

- In C# 7.3, a notable improvement was made regarding how programmers can work with "fixed fields". Previously, working with fixed fields, especially when dealing with unmanaged code or performing operations that require pinning objects in memory, could be cumbersome and required extra steps for safety and performance. The update simplifies these interactions, making it possible to access fixed fields directly without needing to pin them first. This change allows for more efficient code, especially in scenarios where performance is critical, by reducing the overhead and complexity associated with pinning. This enhancement is part of C# 7.3's broader efforts to make safe code perform as efficiently as unsafe code, streamlining development without compromising on safety.

- **Old Way:**

  ```csharp
    unsafe struct Buffer
    {
        public fixed byte data[128];
    }

    class Program
    {
        static unsafe void Main()
        {
            Buffer buffer = new Buffer();

            // Pinning required before C# 7.3
            fixed (byte* ptr = buffer.data)
            {
                // Use the buffer
            }
        }
    }
  ```

- **New Way:**

  ```csharp
     unsafe struct Buffer
     {
         public fixed byte data[128];
     }

     class Program
     {
         static unsafe void Main()
         {
             Buffer buffer = new Buffer();

             // Directly access fixed fields without pinning
             byte value = buffer.data[5]; // Access the 6th element

             // Perform operations as needed without explicit pinning
             buffer.data[5] = 123;
         }
     }
  ```

#### Flexible Ref Local Variables

- In C# 7.3, a new feature was introduced called "Flexible Ref Local Variables." This feature allows programmers to reassign `ref` local variables to point to different locations during their lifetime. Essentially, if you have a variable that references a certain piece of data, you can now change it to reference another piece of data. This adds more versatility and control in handling data that is passed by reference, making code more efficient and easier to manage in certain scenarios.

- **Swapping References**

  ```csharp
     public class RefLocalSwap
     {
         public static void Swap(ref int x, ref int y)
         {
             ref int temp = ref x;
             x = y;
             y = temp;
         }

         public static void Main()
         {
             int a = 1;
             int b = 2;
             Console.WriteLine($"Before swap: a = {a}, b = {b}");

             Swap(ref a, ref b);
             Console.WriteLine($"After swap: a = {a}, b = {b}");
         }
     }
  ```

- **Ref Local Reassignment in a Loop**

  ```csharp
      public class RefLocalLoop
      {
          public static void DoubleArrayElements(int[] numbers)
          {
              for (int i = 0; i < numbers.Length; i++)
              {
                  ref int currentElement = ref numbers[i];
                  currentElement *= 2; // Double the current element
              }
          }

          public static void Main()
          {
              int[] array = { 1, 2, 3, 4, 5 };
              DoubleArrayElements(array);

              Console.WriteLine(string.Join(", ", array));
          }
      }
  ```

- **Selecting a Reference Based on a Condition**

  ```csharp
      public class RefLocalConditional
      {
          public static void IncrementLargerNumber(ref int x, ref int y)
          {
              ref int largerNumber = ref x > y ? ref x : ref y;
              largerNumber++;
          }

          public static void Main()
          {
              int num1 = 5;
              int num2 = 10;
              Console.WriteLine($"Before: num1 = {num1}, num2 = {num2}");

              IncrementLargerNumber(ref num1, ref num2);
              Console.WriteLine($"After: num1 = {num1}, num2 = {num2}");
          }
      }
  ```

#### Stackalloc Array Initializers

- In C# 7.3, a new feature called "Stackalloc Array Initializers" was introduced. This feature allows developers to easily initialize arrays in the stack memory, rather than the heap, by using a familiar syntax. This means you can quickly set up arrays with predefined values right at the moment of their creation, making your code cleaner and more efficient. This feature is particularly useful for high-performance applications where managing memory efficiently is crucial.

- **Basic Initialization**

  ```csharp
      unsafe void InitializeStackAllocatedArray()
      {
          // Allocate an array of 3 integers on the stack and initialize it
          Span<int> numbers = stackalloc int[3] { 1, 2, 3 };

          // Iterate through the array and print its elements
          for(int i = 0; i < numbers.Length; i++)
          {
              Console.WriteLine(numbers[i]);
          }
      }
  ```

- **Using with a Fixed Size Buffer**

  ```csharp
      unsafe struct FixedSizeBufferExample
      {
          public fixed int Buffer[10]; // Fixed size buffer inside a struct

          public void InitializeAndPrint()
          {
              // Allocate and initialize the buffer
              Span<int> bufferSpan = stackalloc int[10] { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };

              // Copying values to the fixed buffer (assuming unsafe context)
              for(int i = 0; i < 10; i++)
              {
                  Buffer[i] = bufferSpan[i];
              }

              // Print the buffer elements
              for(int i = 0; i < 10; i++)
              {
                  Console.WriteLine(Buffer[i]);
              }
          }
      }
  ```

- **Interoperating with Unmanaged Code**

  ```csharp
      unsafe void CallUnmanagedCode()
      {
          // Allocate an array of 5 bytes on the stack
          Span<byte> data = stackalloc byte[5] { 1, 2, 3, 4, 5 };

          // Assuming there's an unmanaged method expecting a pointer to 5 bytes
          // UnmanagedMethod(byte* data, int length);
          // You can call it like this:
          fixed(byte* pData = data)
          {
              UnmanagedMethod(pData, data.Length);
          }
      }
  ```

- **Working with Span<T>**

  ```csharp
      void UseSpanWithStackAlloc()
      {
          // Allocate an array of doubles on the stack and initialize it
          Span<double> temperatures = stackalloc double[4] { 20.5, 22.3, 23.8, 25.0 };

          // Calculate the average temperature
          double sum = 0;
          foreach(var temp in temperatures)
          {
              sum += temp;
          }
          double average = sum / temperatures.Length;

          Console.WriteLine($"Average temperature: {average}°C");
      }
  ```

#### Extended Fixed Statement Usability

- In C# 7.3, the `fixed` statement was enhanced to allow for more flexible and convenient usage. The `fixed` statement is used in unsafe contexts to pin objects in memory, preventing the garbage collector from moving them. This is often necessary when working with unmanaged code or when performance optimizations are required. The improvements in C# 7.3 make it easier to work with fixed buffers and arrays, simplifying the process and reducing the boilerplate code needed to achieve the desired results.

- **Pattern-based `fixed` statement**

  ```csharp
      public class CustomBufferHolder
      {
          private int[] buffer = new int[10];

          // Pattern-based `fixed` statement enhancement
          public ref int GetPinnableReference()
          {
              return ref buffer[0];
          }
      }

      class Program
      {
          unsafe static void Main(string[] args)
          {
              CustomBufferHolder holder = new CustomBufferHolder();

              // Using pattern-based `fixed` statement with custom type
              fixed (int* pBuffer = holder)
              {
                  // pBuffer is now a pointer to the beginning of holder's internal buffer
                  // Safe to use within this fixed block
              }
          }
      }
  ```

- **Indexing movable fixed buffers without pinning**

  ```csharp
      public unsafe struct FixedBufferHolder
      {
          public fixed int fixedBuffer[100];

          public int this[int index]
          {
              get
              {
                  // Safe access to fixed field without needing to pin
                  fixed (int* p = fixedBuffer)
                  {
                      return p[index];
                  }
              }
              set
              {
                  fixed (int* p = fixedBuffer)
                  {
                      p[index] = value;
                  }
              }
          }
      }

      class Program
      {
          unsafe static void Main(string[] args)
          {
              FixedBufferHolder holder = new FixedBufferHolder();

              // Setting and getting values through the indexer
              holder[0] = 42;  // Uses the setter
              int value = holder[0];  // Uses the getter

              System.Console.WriteLine(value);  // Outputs: 42
          }
      }
  ```

#### Expanded Generic Constraints

- This feature allows developers to be more specific about the types they use in their generic methods or classes. For example, they can now specify that a type parameter must be an enum, delegate, or an `unmanaged` type. An `unmanaged` type is a special kind of type that does not contain any references to the managed memory and is safe to use with unmanaged code. This improvement makes the code safer and more efficient, especially when working with low-level programming tasks. It helps developers avoid errors by enforcing stricter type checks at compile time.

- **`enum` Constraint**

  ```csharp
      public class EnumOperations<T> where T : Enum
      {
          public List<string> GetEnumNames()
          {
              return Enum.GetNames(typeof(T)).ToList();
          }

          public T Parse(string value)
          {
              return (T)Enum.Parse(typeof(T), value);
          }
      }

      // Usage
      public enum Rainbow { Red, Orange, Yellow, Green, Blue, Indigo, Violet }

      var rainbowOps = new EnumOperations<Rainbow>();
      Console.WriteLine(string.Join(", ", rainbowOps.GetEnumNames()));
      // Output: Red, Orange, Yellow, Green, Blue, Indigo, Violet

      var color = rainbowOps.Parse("Green");
      Console.WriteLine(color);
      // Output: Green
  ```

- **`Delegate` Constraint**

  ```csharp
      public class DelegateContainer<T> where T : Delegate
      {
          public T DelegateInstance { get; set; }

          public void InvokeDelegate(params object[] args)
          {
              DelegateInstance?.DynamicInvoke(args);
          }
      }

      // Usage
      Action<string> myAction = (message) => Console.WriteLine(message);
      var container = new DelegateContainer<Action<string>> { DelegateInstance = myAction };
      container.InvokeDelegate("Hello, Delegate Constraint!");
      // Output: Hello, Delegate Constraint!
  ```

- **`unmanaged` Constraint**

  ```csharp
      public unsafe struct Buffer<T> where T : unmanaged
      {
          public T[] Items;

          public Buffer(int size)
          {
              Items = new T[size];
          }

          public void SetItem(int index, T value)
          {
              fixed (T* ptr = &Items[0])
              {
                  ptr[index] = value;
              }
          }

          public T GetItem(int index)
          {
              return Items[index];
          }
      }

      // Usage
      var intBuffer = new Buffer<int>(5);
      intBuffer.SetItem(0, 42);
      Console.WriteLine(intBuffer.GetItem(0));
      // Output: 42
  ```

### Incremental Improvements

#### Tuple Equality Operations

- In C# 7.3, a new feature was introduced allowing for the direct comparison of tuple types using the == and != operators. This means you can now check if two tuples are equal or not by comparing their corresponding elements. For example, if you have two tuples representing two sets of data, you can easily check if they contain the same values in the same order. This simplifies code and makes it more readable when working with tuples, as you no longer need to compare each element individually.

- **Basic Tuple Equality and Inequality**

```csharp
    // Example 1: Comparing two tuples for equality
    var tuple1 = (1, "apple");
    var tuple2 = (1, "apple");

    bool areEqual = tuple1 == tuple2; // true

    // Example 2: Comparing two tuples for inequality
    var tuple3 = (1, "apple");
    var tuple4 = (2, "banana");

    bool areNotEqual = tuple3 != tuple4; // true
```

- **Nested Tuple Equality**: Tuple comparisons also work with nested tuples, comparing each element recursively.

```csharp
    // Example 3: Nested tuple comparison
    var nestedTuple1 = (1, ("apple", 5));
    var nestedTuple2 = (1, ("apple", 5));

    bool nestedAreEqual = nestedTuple1 == nestedTuple2; // true

    // Example 4: Nested tuple inequality
    var nestedTuple3 = (1, ("apple", 5));
    var nestedTuple4 = (1, ("apple", 6)); // Notice the difference in the nested value

    bool nestedAreNotEqual = nestedTuple3 != nestedTuple4; // true
```

- **Nullable Tuple Equality**: C# 7.3's tuple equality operations seamlessly handle nullable tuples as well.

```csharp
    // Example 5: Nullable tuple comparison where both are null
    (int, string)? nullableTuple1 = null;
    (int, string)? nullableTuple2 = null;

    bool nullableAreEqual = nullableTuple1 == nullableTuple2; // true, as both are null

    // Example 6: Nullable tuple comparison where one is null
    (int, string)? nullableTuple3 = (1, "apple");
    (int, string)? nullableTuple4 = null;

    bool oneIsNull = nullableTuple3 != nullableTuple4; // true, as one of them is not null
```

- **Using `==` and `!=` with Mixed Tuple Types**: The `==` and `!=` operators are also smart enough to handle comparisons between tuples of compatible types.

```csharp
    // Example 7: Comparing tuples of compatible types
    var compatibleTuple1 = (1, "apple");
    (int, string) compatibleTuple2 = (1, "apple");

    bool compatibleAreEqual = compatibleTuple1 == compatibleTuple2; // true

    // Example 8: Demonstrating type compatibility in tuples
    var mixedTuple1 = (1, 2.0); // int and double
    var mixedTuple2 = (1, 2); // int and int, but compatible due to implicit conversion

    bool mixedAreEqual = mixedTuple1 == mixedTuple2; // true, due to implicit conversion of second element from int to double
```

#### Broader Expression Variable Scope

- In C# 7.3, the scope of expression variables was expanded to include the entire expression in which they are declared. Previously, expression variables were limited to the specific subexpression in which they were defined. This change allows developers to use expression variables more flexibly and access them throughout the entire expression, making code more concise and readable. It also aligns with the language's goal of improving the developer experience and reducing boilerplate code.

- **Old Way:**

  ```csharp
      public static void OldExpressionScope()
      {
          int x = 5;
          int y = 10;

          // Expression variable limited to the specific subexpression
          var result = (x * y) + (x * y);
          Console.WriteLine(result);
      }
  ```

- **Field Initializers**

  ```csharp
      public class FieldInitializerExample
      {
          private static readonly string inputString = "123";
          // Use of expression variable in a field initializer
          private int numericValue = int.TryParse(inputString, out var result) ? result : 0;

          public int NumericValue => numericValue;
      }
  ```

- **Constructor Initializers**

  ```csharp
      public class ConstructorInitializerExample
      {
          public int NumericValue { get; }

          public ConstructorInitializerExample(string input)
              : this(int.TryParse(input, out var result) ? result : 0)
          {
          }

          private ConstructorInitializerExample(int numericValue)
          {
              NumericValue = numericValue;
          }
      }
  ```

- **LINQ Query Expressions**

  ```csharp
      using System;
      using System.Linq;

      public class LinqQueryExample
      {
          public void ProcessNumbers()
          {
              var numbers = new[] { "1", "2", "three", "4", "five" };

              var query = from number in numbers
                          where int.TryParse(number, out var numericValue)
                          select numericValue;

              foreach (var num in query)
              {
                  Console.WriteLine(num);
              }
          }
      }
  ```

#### Attributes on Auto-Implemented Property Backing Fields

- In C# 7.3, a new feature was introduced allowing developers to apply attributes directly to the backing fields of auto-implemented properties. Previously, if you wanted to attach an attribute to a property's backing field, you had to manually define the field and property. This process was more verbose and time-consuming. The update simplifies this by allowing the use of attributes with the `field:` target prefix directly on auto-implemented properties. This means you can now easily apply attributes like `[NonSerialized]` to the hidden backing field of an auto-implemented property, enhancing control over serialization and other behaviors directly in your property definitions, without additional boilerplate code.

- **Applying NonSerialized Attribute**

  ```csharp
      using System;

      [Serializable]
      public class Person
      {
          // Auto-implemented property with NonSerialized attribute on its backing field
          [field: NonSerialized]
          public string SocialSecurityNumber { get; set; }

          public string Name { get; set; }

          public Person(string name, string ssn)
          {
              Name = name;
              SocialSecurityNumber = ssn;
          }
      }
  ```

- **Applying Compiler-Generated Attributes**

  ```csharp
      using System.Diagnostics;
      using System.Runtime.CompilerServices;

      public class DebugExample
      {
          // Hide the backing field from debugger view
          [field: DebuggerBrowsable(DebuggerBrowsableState.Never)]
          public int HiddenValue { get; set; }

          // Mark the backing field as compiler-generated
          [field: CompilerGenerated]
          public string CompilerGeneratedExample { get; set; }
      }
  ```

- **Custom Attributes for Validation or Metadata**

  ```csharp
      public class MetadataExample
      {
          [field: Metadata("This property represents an age.")]
          public int Age { get; set; }
      }

      [AttributeUsage(AttributeTargets.Field)]
      public class MetadataAttribute : Attribute
      {
          public string Description { get; }

          public MetadataAttribute(string description)
          {
              Description = description;
          }
      }
  ```

- **Attributes for ORM Mapping**

  ```csharp
      using System.ComponentModel.DataAnnotations;
      using System.ComponentModel.DataAnnotations.Schema;

      public class EntityExample
      {
          [field: Key]
          public int Id { get; set; }

          [field: Column("full_name")]
          public string Name { get; set; }
      }
  ```

#### Refined Method Resolution Logic

- In C# 7.3, the method resolution logic has been improved to make it easier for developers to work with overloaded methods. This update focuses on ensuring that the most appropriate method is selected when multiple options are available, reducing the chances of encountering errors or ambiguities. The main adjustments include better handling of static and instance members, removing generic methods that don't meet type constraints from consideration, and refining how method group conversions are processed. These enhancements aim to streamline the coding experience, making it more intuitive and error-free when calling methods, especially those that are overloaded.

- **Static vs. Instance Members**

  ```csharp
      class SampleMethodResolution
      {
          public static void Display() => Console.WriteLine("Static Display");
          public void Display(int i) => Console.WriteLine($"Instance Display: {i}");

          public static void Test()
          {
              // Prior to C# 7.3, this might have been ambiguous.
              // With C# 7.3, the compiler correctly picks the static method.
              Display();
          }
      }
  ```

- **Generic Constraints**

  ```csharp
      class GenericMethodResolution
      {
          public static void Process<T>(T item) where T : class
          {
              Console.WriteLine($"Processing class: {item.GetType().Name}");
          }

          public static void Process<T>(T? item) where T : struct
          {
              Console.WriteLine($"Processing struct: {typeof(T).Name}");
          }

          public static void Test()
          {
              Process("Hello"); // Picks the first Process method (class constraint).
              Process<int>(5); // Picks the second Process method (struct constraint).
          }
      }
  ```

- **Method Group Conversion**

  ```csharp
      class MethodGroupConversion
      {
          public static int CalculateSum(int a, int b) => a + b;
          public static void DisplaySum(int a, int b) => Console.WriteLine($"Sum: {a + b}");

          public static void Test()
          {
              Func<int, int, int> del = CalculateSum;
              // DisplaySum would not be considered a candidate for the Func delegate,
              // because its return type void doesn't match the expected return type int.
              // This avoids potential ambiguity or incorrect method selection.
          }
      }
  ```

## Conclusion

In conclusion, C# 7.3 marks a significant step forward in the evolution of the C# programming language, introducing a wealth of features that enhance performance, provide more flexibility, and streamline coding practices. From performance enhancements in safe code that allow for more efficient memory management and direct field access, to expanded generic constraints that offer greater control over type safety and code behavior, this version of C# empowers developers to write more robust and high-performing applications.

Furthermore, incremental improvements like tuple equality operations and broader expression variable scopes make the language more intuitive and enjoyable to use, while updates to method resolution logic ensure that the most appropriate methods are called in overloaded scenarios, reducing errors and improving code clarity.

Overall, C# 7.3 embodies the ongoing commitment to language innovation, providing developers with the tools they need to meet the challenges of modern software development. Whether you are optimizing performance-critical code, leveraging new language features to reduce boilerplate, or ensuring type safety with enhanced constraints, C# 7.3 offers a compelling array of new capabilities to explore and integrate into your projects.
