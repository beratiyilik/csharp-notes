# C# 7.2 New Features Guide

## Table of Contents

- [C# 7.2 New Features Guide](#c-72-new-features-guide)
  - [Table of Contents](#table-of-contents)
  - [Introduction and Overview](#introduction-and-overview)
  - [New Features](#new-features)
    - [Leading Underscores in Numeric Literals](#leading-underscores-in-numeric-literals)
    - [Non-trailing Named Arguments](#non-trailing-named-arguments)
    - [Contiguous Memory Accessor for Stack-Allocated Structures `Span<T>`](#contiguous-memory-accessor-for-stack-allocated-structures-spant)
    - [Private Protected Access Modifier](#private-protected-access-modifier)
    - [Ref Conditional Expression](#ref-conditional-expression)
    - [Ref extension methods on structs](#ref-extension-methods-on-structs)
    - [Readonly Ref](#readonly-ref)
  - [Conclusion](#conclusion)

## Introduction and Overview

In C# 7.2, new features make coding more efficient and clear. These include leading underscores in numeric literals for better readability, non-trailing named arguments that allow for flexible method calls, and the `Span<T>` type for efficient memory use. Additionally, the `private protected` access modifier offers precise control over class access, while `ref` enhancements enable powerful data manipulation. `readonly ref` ensures data integrity when sharing. These updates simplify complex tasks, ensuring code is more manageable and performant.

## New Features

### Leading Underscores in Numeric Literals

- In C# 7.2, a small but helpful feature was introduced for numeric literals: leading underscores. Before this update, you could already use underscores within numeric literals to make them easier to read, like separating groups of digits in large numbers. However, C# 7.2 allows you to place underscores right after the base specifier of binary `(0b)` and hexadecimal `(0x)` literals. This means you can now write numbers in a way that visually separates the base specifier from the number itself, making your code clearer. For example, binary and hexadecimal numbers can start with `0b_` and `0x_` respectively, followed by the actual digits with optional additional underscores for further separation. This change is all about making your numeric literals more readable and maintainable.

  ```csharp
      int million = 1_000_000;
      Console.WriteLine(million);  // Output: 1000000
  ```

- **Old Way**: Hexadecimal, digit separators could be used as follows

  ```csharp
      int million = 1000000;
      Console.WriteLine(million);  // Output: 1000000
  ```

- **New Way**: Hexadecimal, you can now also place an underscore right after the `0x`

  ```csharp
      int color = 0x_FF_FF_FF;
      Console.WriteLine(color);  // Output: 16777215
  ```

- **Old Way**: Binary, binary literals could be written with digit separators for readability

  ```csharp
      int bValue = 0b1010_1011_1100_1101;
      Console.WriteLine(bValue);  // Output: 43981
  ```

- **New Way**: Binary, allows leading underscores right after the `0b`

  ```csharp
      int bValue = 0b_1010_1011_1100_1101;
      Console.WriteLine(bValue);  // Output: 43981
  ```

- **Representing a Bit Field**

  ```csharp
    [Flags]
    public enum FileAccess
    {
        Read = 0b_0001,
        Write = 0b_0010,
        Execute = 0b_0100,
        ReadWrite = Read | Write,
        All = Read | Write | Execute
    }

    Console.WriteLine(FileAccess.ReadWrite); // Output: ReadWrite
    Console.WriteLine((int)FileAccess.All); // Output: 7
  ```

- **Defining Color Constants**

  ```csharp
    public static class Colors
    {
        public const int Red = 0xFF_00_00;
        public const int Green = 0x00_FF_00;
        public const int Blue = 0x00_00_FF;
    }

    Console.WriteLine(Colors.Red); // Output: 16711680
    Console.WriteLine(Colors.Green); // Output: 65280
    Console.WriteLine(Colors.Blue); // Output: 255
  ```

### Non-trailing Named Arguments

- In C# 7.2, a new feature called "Non-trailing Named Arguments" was introduced. This feature allows you to specify arguments for a method in any order by naming them, even if they are not at the end of the argument list. Previously, when using named arguments, any positional arguments had to come first, followed by the named ones. With this update, you can mix named and positional arguments freely as long as the named arguments are placed in their correct positions according to the method's parameter list. This makes the code more readable and flexible, especially in cases where you want to specify only some arguments by name for clarity while leaving others in their positional order.

  ```csharp
    void SendEmail(string to, string subject, string body, bool ccAdmin = false);

    // Invoking the method without using Non-trailing Named Arguments
    SendEmail("example@example.com", "Hello World", "This is a test email.", ccAdmin: true);

    // Invoking the method with Non-trailing Named Arguments, allowing the 'ccAdmin' named argument to be specified before 'subject' and 'body'
    SendEmail("example@example.com", ccAdmin: true, subject: "Hello World", body: "This is a test email.");
  ```

  ```csharp
    void CreateReport(string title, bool includeHeader = true, bool includeFooter = false, string dateFormat = "MM/dd/yyyy");

    // Invoking the method without using Non-trailing Named Arguments
    CreateReport("Annual Report", true, includeFooter: true, dateFormat: "yyyy-MM-dd");

    // Invoking the method with Non-trailing Named Arguments, allowing the 'includeFooter' and 'dateFormat' named arguments to be specified without mentioning 'includeHeader'
    CreateReport("Annual Report", includeFooter: true, dateFormat: "yyyy-MM-dd");
  ```

- **Usage in Object Initializers**

  ```csharp
    // Initializing a new object without using Non-trailing Named Arguments
    var settings = new ReportSettings
    {
        Title = "Annual Sales Report",
        IncludeHeader = true,
        IncludeFooter = true,
        DateFormat = "yyyy-MM-dd"
      };

    // Initializing a new object with Non-trailing Named Arguments to specify only certain properties, making the code more concise and focused on non-default values
    var settings = new ReportSettings
    {
        Title = "Annual Sales Report",
        IncludeFooter: true,
        DateFormat: "yyyy-MM-dd"
    };
  ```

### Contiguous Memory Accessor for Stack-Allocated Structures `Span<T>`

- In C# 7.2, a new feature called `Span<T>` was introduced. This feature is a special kind of structure that allows for more efficient handling of certain kinds of data, such as arrays or slices of memory. Unlike regular structures, `Span<T>` is a "stack-only" type, meaning it is designed to be used and stored in a method's local memory space rather than on the heap, which is used for longer-lived data.

- The main advantage of `Span<T>` is that it provides a way to access a continuous region of memory, such as an array, without the need for copying or allocating additional memory. This can lead to significant performance improvements in applications that process large amounts of data or perform complex memory operations.

- `Span<T>` is referred to by several names, including "interior pointer" and "stackonly struct." These names highlight its unique characteristics: it acts like a pointer to data within another data structure and is constrained to live only on the stack. This constraint ensures that `Span<T>` instances are always used in a safe manner, avoiding issues related to memory management and garbage collection that can affect application performance.

- Overall, the introduction of `Span<T>` in C# 7.2 provides developers with a powerful tool for improving the efficiency of memory operations in their applications, making it easier to work with data in a high-performance manner.

- Basic Usage of `Span<T>`: This example shows how to create a `Span<T>` from an array. We change the first element and slice part of the `Span`. This demonstrates that changes in `Span` affect the original array.

  ```csharp
  using System;

  public class SpanExample
  {
      public static void Main()
      {
          int[] array = new int[] { 1, 2, 3, 4, 5 };

          // Create a Span<int> over the array.
          Span<int> span = new Span<int>(array);

          // Modify the contents of the Span.
          span[0] = 10;

          // Show the changes are reflected in the original array.
          Console.WriteLine(string.Join(", ", array));  // Output: 10, 2, 3, 4, 5

          // Slice the Span to get a sub-span.
          Span<int> slice = span.Slice(1, 3);

          // Show the contents of the slice.
          Console.WriteLine(string.Join(", ", slice.ToArray()));  // Output: 2, 3, 4
      }
  }
  ```

- Using `Span<T>` with `stackalloc`: Here, we show how to use `stackalloc` with `Span<T>`. This creates a `Span` directly in memory, which is fast. We modify it and show the results.

  ```csharp
  using System;

  public class SpanStackAllocExample
  {
      public static void Main()
      {
          // Allocate a block of memory on the stack.
          Span<int> stackAllocSpan = stackalloc int[5] { 1, 2, 3, 4, 5 };

          // Modify the contents of the Span.
          stackAllocSpan[0] = 10;

          // Iterate over the Span and print each element.
          foreach (int value in stackAllocSpan)
          {
              Console.WriteLine(value);  // Output: 10, 2, 3, 4, 5
          }
      }
  }
  ```

- `Span<T>` with Methods and `ref struct`: This example uses a `Span<int>` in a method and a `ref struct`. It shows how to change Span contents inside a method and how `ref struct` can work with `Span`.

  ```csharp
  using System;

  public class SpanWithMethods
  {
      public static void Main()
      {
          int[] array = new int[] { 1, 2, 3, 4, 5 };

          // Creating a Span<int> from an array.
          Span<int> span = new Span<int>(array);

          // Pass the Span to a method to modify its contents.
          DoubleSpanValues(span);

          Console.WriteLine(string.Join(", ", array));  // Output: 2, 4, 6, 8, 10
      }

      // Method that doubles each value in the passed Span<int>.
      public static void DoubleSpanValues(Span<int> span)
      {
          for (int i = 0; i < span.Length; i++)
          {
              span[i] *= 2;
          }
      }
  }

  // Demonstrating a ref struct.
  public ref struct RefStructExample
  {
      private Span<int> internalSpan;

      public RefStructExample(Span<int> span)
      {
          this.internalSpan = span;
      }

      public void ModifySpan(int newValue)
      {
          // Modify the first element of the internal Span, if possible.
          if (internalSpan.Length > 0)
          {
              internalSpan[0] = newValue;
          }
      }
  }
  ```

- `ReadOnlySpan<T>` for Read-Only Access: This last example shows how to create a `ReadOnlySpan<char>` from a string. We can look at the characters without changing them, which is useful for reading data safely.

  ```csharp
    using System;

    public class ReadOnlySpanExample
    {
        public static void Main()
        {
            string str = "Hello, Span!";

            // Create a ReadOnlySpan<char> from a string.
            ReadOnlySpan<char> readOnlySpan = str.AsSpan();

            // Display the contents of the ReadOnlySpan.
            foreach (char c in readOnlySpan)
            {
                Console.Write(c);  // Output: Hello, Span!
            }
        }
    }
  ```

- These examples showcase the versatility of `Span<T>` and `ReadOnlySpan<T>` in C# 7.2, providing efficient manipulation of contiguous memory regions, support for stack-allocated memory with `stackalloc`, and enforcing immutability with `ReadOnlySpan<T>`.

### Private Protected Access Modifier

- In C# 7.2, a new feature called "Private Protected Access Modifier" was introduced. This access modifier combines the properties of both `protected` and `internal` modifiers. It allows a class member to be accessed by derived classes located in the same assembly. This means that if you have a class with a member marked as `private protected`, this member can be used in subclasses but only if these subclasses are within the same assembly. It provides a more specific level of access control, offering a blend of the protection and internal visibility, making it useful for more controlled inheritance scenarios.

- **Basic Example**: Consider a base class in an assembly that uses the `private protected` access modifier

  ```csharp
  public class BaseClass
  {
      private protected void Display()
      {
          Console.WriteLine("BaseClass Display");
      }
  }
  ```

A derived class within the same assembly can access the `Display` method:

```csharp
    public class DerivedClass : BaseClass
    {
        public void ShowDisplay()
        {
            Display(); // OK: Accessible within derived class in the same assembly
        }
    }
```

However, if a class tries to access the `Display` method from a non-derived context within the same assembly, it will result in an error:

```csharp
   public class NonDerivedClass
   {
       public void AttemptAccess()
       {
           var baseClassInstance = new BaseClass();
           //baseClassInstance.Display(); // Error: Not accessible
       }
   }
```

- **Access from a Different Assembly**: When trying to access a `private protected` member from a derived class in a different assembly, access is denied, which contrasts with the `protected internal` modifier that would allow such access.

Assuming `BaseClass` is defined in AssemblyA, and we have the following in AssemblyB:

```csharp
    // In a different assembly
    public class DerivedClassInDifferentAssembly : AssemblyA.BaseClass
    {
        public void TryToShowDisplay()
        {
            //Display(); // Error: Not accessible from a derived class in a different assembly
        }
    }
```

- **Real-world Scenario**: Consider you are building a library that involves rendering shapes. You might have a base class `Shape` with common rendering logic and several derived classes like `Circle`, `Square`, etc. You want to ensure that certain rendering details are only modifiable by classes in your library and only by those that derive from `Shape`:

```csharp
   // In your shapes library assembly
   public abstract class Shape
   {
       private protected void ApplyBaseRendering()
       {
           Console.WriteLine("Applying base rendering configurations");
       }
   }

   public class Circle : Shape
   {
       public void Render()
       {
           ApplyBaseRendering(); // OK: Accessible
           Console.WriteLine("Rendering a circle");
       }
   }
```

This setup ensures that only your shapes can apply the base rendering configurations, keeping these details encapsulated within your library's inheritance hierarchy.

### Ref Conditional Expression

- In C# 7.2, a new feature called "Ref Conditional Expression" was introduced. This feature allows programmers to choose between two references based on a condition, similar to how you might choose between two values with a conditional operator. Before, this wasn't possible with references. Now, you can write more flexible code by using a condition to decide which reference to use in your operations. This is especially useful when you want to work directly with the memory locations of variables for efficiency or when dealing with large data structures.

- **Basic Usage of Ref Conditional Expression**

  ```csharp
  public static void SwapIfCondition<T>(ref T first, ref T second, bool condition)
  {
      // Swap the references of first and second if condition is true
      ref T temp = ref condition ? ref first : ref second;
      T originalFirst = first;
      first = second;
      second = originalFirst;
  }

  static void Main(string[] args)
  {
      int x = 1, y = 2;
      Console.WriteLine($"Before swap: x = {x}, y = {y}");

      SwapIfCondition(ref x, ref y, true);
      Console.WriteLine($"After swap: x = {x}, y = {y}");
  }
  ```

- **Ref Conditional Expression with Arrays**

  ```csharp
  public static ref int FindOrFallback(ref int[] arr, int index, ref int fallback)
  {
      // Return a reference to the array element at the specified index
      // or a reference to a fallback value if the index is out of bounds
      return ref (index >= 0 && index < arr.Length) ? ref arr[index] : ref fallback;
  }

  static void Main(string[] args)
  {
      int[] numbers = { 10, 20, 30 };
      int fallback = -1;
      ref int refNumber = ref FindOrFallback(ref numbers, 1, ref fallback);

      Console.WriteLine($"Found: {refNumber}");

      // Modifying through the reference
      refNumber = 25;
      Console.WriteLine($"Modified numbers[1]: {numbers[1]}");
  }
  ```

- **Combining with Local Functions**

```csharp
    public static void ProcessData(int[] data, ref int threshold)
    {
        ref int GetRefElement(int index) => ref (index >= 0 && index < data.Length) ? ref data[index] : ref threshold;

        // Use the local function to safely access or modify elements
        ref int element = ref GetRefElement(2);
        Console.WriteLine($"Element before: {element}");
        element = 100;  // Modify through the reference
        Console.WriteLine($"Element after: {data[2]}");
    }

    static void Main(string[] args)
    {
        int[] myData = { 1, 2, 3, 4 };
        int myThreshold = 10;
        ProcessData(myData, ref myThreshold);
    }
```

- **Ref Conditional Expression with Structs**

  ```csharp
     public struct MutablePoint
     {
         public int X, Y;

         public MutablePoint(int x, int y)
         {
             X = x;
             Y = y;
         }
     }

     public static ref MutablePoint GetPointOrFallback(ref MutablePoint[] points, int index, ref MutablePoint fallback)
     {
         // Return a reference to the specified point or a fallback if index is out of range
         return ref (index >= 0 && index < points.Length) ? ref points[index] : ref fallback;
     }

     static void Main(string[] args)
     {
         MutablePoint fallback = new MutablePoint(-1, -1);
         MutablePoint[] pointArray = { new MutablePoint(0, 0), new MutablePoint(1, 1) };

         ref MutablePoint refPoint = ref GetPointOrFallback(ref pointArray, 0, ref fallback);
         Console.WriteLine($"Point before: X = {refPoint.X}, Y = {refPoint.Y}");

         // Modify through the reference
         refPoint.X = 10; refPoint.Y = 20;
         Console.WriteLine($"Point after: X = {pointArray[0].X}, Y = {pointArray[0].Y}");
     }
  ```

### Ref extension methods on structs

- In C# 7.2, a new feature called "ref extension methods on structs" was introduced. This feature allows methods to be extended by reference, specifically for structures (structs). Normally, extension methods enable adding new methods to existing types without altering the type itself. The "ref" keyword in this context means that the method operates directly on the original structure rather than on a copy of it. This is particularly useful for large structures where copying can be expensive in terms of performance. It allows for more efficient code execution, especially when modifying large structs, by avoiding unnecessary copying of data.

- **Mutating a struct instance with `ref` extension method**

```csharp
    public struct Point
    {
        public double X, Y;

        public Point(double x, double y)
        {
            X = x;
            Y = y;
        }
    }

    public static class PointExtensions
    {
        // Ref extension method that mutates the struct instance
        public static void Offset(ref this Point point, double dx, double dy)
        {
            point.X += dx;
            point.Y += dy;
        }
    }

    class Program
    {
        static void Main()
        {
            Point p = new Point(1, 1);
            // Using the ref extension method to mutate the struct instance
            p.Offset(1, 1);

            Console.WriteLine($"Point after offset: ({p.X}, {p.Y})");
            // Output: Point after offset: (2, 2)
        }
    }
```

- **Reading a struct instance with `in` extension method**

```csharp
    public static class PointExtensions
    {
        // In extension method that reads from the struct instance without modifying it
        public static double DistanceToOrigin(in this Point point)
        {
            return Math.Sqrt(point.X * point.X + point.Y * point.Y);
        }
    }

    class Program
    {
        static void Main()
        {
            Point p = new Point(3, 4);
            // Using the in extension method to calculate distance without modifying the struct
            double distance = p.DistanceToOrigin();

            Console.WriteLine($"Distance to origin: {distance}");
            // Output: Distance to origin: 5
        }
    }
```

- **Using `in` extension method to avoid unnecessary copying**

```csharp
    public struct LargeStruct
    {
        // Assume this struct has a lot of fields making it large in size
        public int[] Data;

        public LargeStruct(int dataSize)
        {
            Data = new int[dataSize];
        }
    }

    public static class LargeStructExtensions
    {
        // Extension method that reads large struct efficiently without copying
        public static int GetDataSize(in this LargeStruct largeStruct)
        {
            return largeStruct.Data.Length;
        }
    }

    class Program
    {
        static void Main()
        {
            LargeStruct largeStruct = new LargeStruct(10000);
            // Efficiently reading data size without copying the large struct instance
            int size = largeStruct.GetDataSize();

            Console.WriteLine($"Data size: {size}");
            // Output: Data size: 10000
        }
    }
```

### Readonly Ref

- In C# 7.2, a new feature called "Readonly ref" was introduced. This feature allows you to create references to data that cannot be modified. Normally, when you pass data using references, the data can be changed by the receiver. However, with "Readonly ref," you ensure that the data you share cannot be altered, providing a way to safely share information without the risk of unexpected modifications. This feature is useful when you want to maintain the integrity of your data while allowing others to access it.

- **Using `readonly ref` Return**: This code shows how to return a `readonly` reference to a struct. A `readonly` reference means you can look but not change the value. We define a `Point` struct with `X` and `Y` values, and a special `Origin` point that's always at `(0, 0)`. When we use `Origin`, we can see its `X` and `Y`, but we can't change them because it's `readonly`.

  ```csharp
  public readonly struct Point
  {
      public int X { get; }
      public int Y { get; }

      public Point(int x, int y)
      {
          X = x;
          Y = y;
      }

      // Returning a readonly ref to a struct instance
      public static ref readonly Point Origin
      {
          get
          {
              static readonly Point origin = new Point(0, 0);
              return ref origin;
          }
      }
  }

  class ExampleUsage
  {
      public void Demonstrate()
      {
          // Accessing a readonly ref return value
          ref readonly var pointRef = ref Point.Origin;

          Console.WriteLine($"Origin is at X: {pointRef.X}, Y: {pointRef.Y}");

          // This line would cause a compile-time error, as pointRef is readonly
          // pointRef.X = 10;
      }
  }
  ```

- **Passing and Returning `readonly ref` in Methods**: This example uses a `readonly ref` to return and work with a part of an array without making a full copy of it, which saves memory. We create a `Container` class that has an array of `ReadonlyIntWrapper` structs. The `GetItem` method lets us access an item from the array as a `readonly ref`, which means we can see the item but can't change it.

  ```csharp
     public readonly struct ReadonlyIntWrapper
     {
         private readonly int value;

         public ReadonlyIntWrapper(int value)
         {
             this.value = value;
         }

         public int Value => value;
     }

     public class Container
     {
         private ReadonlyIntWrapper[] array = new ReadonlyIntWrapper[10];

         public Container()
         {
             for (int i = 0; i < array.Length; i++)
             {
                 array[i] = new ReadonlyIntWrapper(i);
             }
         }

         // Method returning a readonly reference to an element
         public ref readonly ReadonlyIntWrapper GetItem(int index)
         {
             return ref array[index];
         }
     }

     class ExampleUsage
     {
         public void Demonstrate()
         {
             var container = new Container();
             ref readonly var itemRef = ref container.GetItem(5);

             Console.WriteLine($"Item at index 5 is: {itemRef.Value}");

             // The following line is not allowed, as itemRef is a readonly reference
             // itemRef.Value = 100;
         }
     }
  ```

- **`readonly ref` with `in` Parameter Modifier**: Here, we see how to use `in` to pass a large struct to a method more efficiently. The `LargeStruct` is big, so copying it every time we call a method could slow things down. By using `in`, we pass a reference to it instead of copying. The method `Sum` can read from `LargeStruct` but can't change it, ensuring our data stays safe and unchanged.

  ```csharp
    public readonly struct LargeStruct
    {
        public readonly long A;
        public readonly long B;
        public readonly long C;

        public LargeStruct(long a, long b, long c)
        {
            A = a;
            B = b;
            C = c;
        }
    }

    public class Processor
    {
        // Method that takes a readonly reference to a large struct
        public long Sum(in LargeStruct largeStruct)
        {
            return largeStruct.A + largeStruct.B + largeStruct.C;
        }
    }

    class ExampleUsage
    {
        public void Demonstrate()
        {
            var largeStruct = new LargeStruct(1, 2, 3);
            var processor = new Processor();

            long result = processor.Sum(in largeStruct);

            Console.WriteLine($"Sum is: {result}");

            // Not allowed - cannot modify largeStruct through an in parameter
            // largeStruct.A = 10;
        }
    }
  ```

## Conclusion

C# 7.2 introduces a suite of features enhancing code clarity, efficiency, and safety. With additions like leading underscores in numeric literals and non-trailing named arguments, developers gain tools for more readable and flexible coding practices. The introduction of `Span<T>` marks a significant leap in memory management, optimizing performance in data-intensive applications. Furthermore, the new access modifier and `ref` enhancements fortify the language's capability in encapsulation and data manipulation. Finally, `readonly ref` addresses data integrity, ensuring safer code sharing and manipulation. Collectively, these advancements fortify C#'s standing as a robust, modern programming language tailored for contemporary software development challenges.
