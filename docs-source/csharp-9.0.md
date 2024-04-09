# C# 9.0 New Features

## Table of Contents

- [C# 9.0 New Features](#c-90-new-features)
  - [Table of Contents](#table-of-contents)
  - [Introduction and Overview](#introduction-and-overview)
  - [New Features](#new-features)
    - [Records](#records)
    - [Init-only properties](#init-only-properties)
    - [Top-level statements](#top-level-statements)
    - [Pattern matching enhancements: relational patterns and logical patterns](#pattern-matching-enhancements-relational-patterns-and-logical-patterns)
    - [Performance and interop](#performance-and-interop)
    - [Native sized integers](#native-sized-integers)
    - [Function pointers](#function-pointers)
    - [Suppress emitting `localsinit` flag](#suppress-emitting-localsinit-flag)
    - [Module initializers](#module-initializers)
    - [New features for partial methods](#new-features-for-partial-methods)
    - [Target-typed new expressions](#target-typed-new-expressions)
    - [Static anonymous functions](#static-anonymous-functions)
    - [Target-typed conditional expressions](#target-typed-conditional-expressions)
    - [Covariant return types](#covariant-return-types)
    - [Extension GetEnumerator support for foreach loops](#extension-getenumerator-support-for-foreach-loops)
    - [Lambda discard parameters](#lambda-discard-parameters)
    - [Attributes on local functions](#attributes-on-local-functions)
  - [Conclusion](#conclusion)

## Introduction and Overview

- In C# 9.0, several significant features were introduced, enhancing the language's capabilities and developer productivity. This version emphasizes simplifying common coding scenarios, supporting immutable data models, and enhancing pattern matching for more expressive and concise code. Notably, C# 9.0 brings in records for creating immutable reference types with value-based equality, enabling more predictable code, especially in multithreaded environments. Init-only properties allow for immutable object creation with object initializers, providing a flexible yet secure way to work with data that does not change after its initial creation.

- Top-level statements reduce the boilerplate code required for simple programs, making it easier to get started with C# projects. The introduction of pattern matching enhancements, including relational and logical patterns, offers more powerful and readable conditional logic constructions. Performance improvements for high-performance computing scenarios are achieved through native sized integers and function pointers, facilitating efficient interoperation with unmanaged code and memory operations. The suppression of the `localsinit` flag and the addition of module initializers further contribute to performance optimization and flexibility in initializing modules.

- Furthermore, C# 9.0 extends its language features with target-typed new expressions, static anonymous functions, and covariant return types, among others, providing developers with more tools to write efficient and clean code. The expansion of pattern matching capabilities and the introduction of records represent a significant step towards supporting modern programming patterns, such as immutable data structures, which are essential for concurrent programming models. Overall, C# 9.0 continues to evolve the language, making it more powerful, expressive, and suited to a wide range of programming tasks.

## New Features

### Records

- C# 9.0 introduces a new type called "records," designed to make it easier to create immutable reference types. Unlike traditional classes where equality is based on object reference, records offer value-based equality. This means two record instances are considered equal if they have the same values for all their properties, regardless of whether they are the same instance.

- Records are particularly useful for defining data models where you do not expect the data to change once it's been created. This immutability is achieved through the use of "init" properties, which can only be set during object initialization. After a record is created, its properties cannot be changed, promoting safer and more predictable code, especially in multithreaded environments.

- This feature streamlines data modeling, especially for applications like data transfer objects (DTOs), where records can provide a concise, readable syntax for defining data structures, along with built-in behavior for value equality comparisons.

- **Basic Record Example**

```csharp
public record Person(string FirstName, string LastName);
```

- Illustrates a simple use of records to define immutable reference types with value-based equality.

- **Init-only Properties with Records**

```csharp
public record Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

- Uses `init` accessors for properties to ensure immutability.

- **Record with Methods**

```csharp
public record Person(string FirstName, string LastName)
{
    public string FullName => $"{FirstName} {LastName}";
}
```

- Demonstrates including methods and additional properties in a record definition.

- **Inheritance in Records**

```csharp
public record Person(string FirstName, string LastName);
public record Student(string FirstName, string LastName, int Grade) : Person(FirstName, LastName);
```

- Shows a `Student` record inheriting from a `Person` record, adding an `Grade` property.

- **Positional Records and Deconstruction**

```csharp
public record Person(string FirstName, string LastName);

var person = new Person("John", "Doe");
var (firstName, lastName) = person;
```

- Positional records support deconstruction into individual variables.

- **With-expressions for Non-destructive Mutation**

```csharp
var originalPerson = new Person("Jane", "Doe");
var updatedPerson = originalPerson with { FirstName = "John" };
```

- `with` expressions create a new record instance based on an existing one with modified properties.

- **Value Equality**

```csharp
var person1 = new Person("John", "Doe");
var person2 = new Person("John", "Doe");
Console.WriteLine(person1 == person2); // Outputs: True
```

- Records have value-based equality, where two instances are equal if their properties have equal values.

### Init-only properties

- In C# 9.0, a new feature called "init-only properties" allows properties of an object to be set at the time of creation but remain immutable afterward. This feature provides a more straightforward and flexible way to create immutable objects compared to the traditional approach, which required read-only properties that were only settable through a constructor. With init-only properties, you can easily assign values to properties using object initializers, making the code more concise and readable. This enhancement is part of C# 9.0's broader efforts to improve support for immutable data models and functional programming patterns, ensuring that once an object is fully initialized, its state cannot be modified, which can lead to safer and more predictable code.

- **Basic Init-Only Property in a Person Class**

- This example demonstrates a simple use of init-only properties in a `Person` class to create an immutable object once it's constructed.

```csharp
public class Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }

    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}

var person = new Person("John", "Doe")
{
    // Initialization is allowed here
    FirstName = "Jane"
};

// This will cause a compilation error as FirstName is an init-only property
// person.FirstName = "Jack";
```

- **Using Init-Only Properties with Object Initializers**

- In this example, the init-only properties are being set using an object initializer, showcasing the flexibility and readability of init-only properties for setting up immutable objects.

```csharp
public class Address
{
    public string City { get; init; }
    public string Country { get; init; }
}

var address = new Address
{
    City = "New York",
    Country = "USA"
};

// Modifying the properties after initialization will result in a compile-time error
// address.City = "Los Angeles";
```

- **Init-Only Properties with Records**

- This example shows how init-only properties are used in conjunction with records, a new reference type in C# 9, to enforce immutability more naturally.

```csharp
public record Product
{
    public string Name { get; init; }
    public decimal Price { get; init; }
}

var product = new Product
{
    Name = "Laptop",
    Price = 1200M
};

// Attempting to modify the init-only property will cause a compilation error
// product.Price = 1100M;
```

- **Using Init-Only Properties to Ensure Valid Data**

- Init-only properties can ensure that once an object is fully initialized, its state remains valid and unchanged.

```csharp
public class Account
{
    private readonly decimal _minimumBalance = 100;

    public string AccountNumber { get; init; }
    public decimal Balance { get; init; }

    public Account(string accountNumber, decimal balance)
    {
        if (balance < _minimumBalance)
            throw new ArgumentOutOfRangeException(nameof(balance), "Balance cannot be less than minimum balance.");

        AccountNumber = accountNumber;
        Balance = balance;
    }
}

var account = new Account("123456", 500)
{
    // Initialization with validation
    Balance = 200
};

// Modifying after initialization is not allowed
// account.Balance = 50;
```

-

### Top-level statements

- In C# 9.0, top-level statements make writing simple programs and scripts more straightforward. You no longer need to define a `namespace`, a `Program` class, or a `Main` method. Instead, you can directly write your main program's logic at the top level of a file. This feature reduces the boilerplate code required for small programs, making the code easier to read and write, especially for beginners or for those writing small utilities and scripts. It simplifies the entry point of applications by allowing the immediate execution of code.

- **Basic Console Application**

- Before C# 9.0, a simple console application to print "Hello, World!" would require defining a class and a `Main` method:

```csharp
using System;

namespace HelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello, World!");
        }
    }
}
```

- With C# 9.0, using top-level statements, the same can be achieved with less boilerplate code:

```csharp
using System;

Console.WriteLine("Hello, World!");
```

- **Asynchronous Execution**

- Top-level statements also support asynchronous operations directly. Before C# 9.0, running an asynchronous method from the `Main` method required additional boilerplate:

```csharp
using System;
using System.Threading.Tasks;

namespace AsyncExample
{
    class Program
    {
        static async Task Main(string[] args)
        {
            await Task.Delay(1000); // Simulate asynchronous operation
            Console.WriteLine("Async operation completed");
        }
    }
}
```

- In C# 9.0, this can be simplified using top-level statements:

```csharp
using System;
using System.Threading.Tasks;

await Task.Delay(1000); // Simulate asynchronous operation
Console.WriteLine("Async operation completed");
```

- **Accessing Command Line Arguments**

- Command line arguments are directly accessible in top-level statements without needing to define them explicitly in a `Main` method:

- Before C# 9.0:

```csharp
using System;

namespace CommandLineArgs
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine($"Argument count: {args.Length}");
        }
    }
}
```

- With C# 9.0, using top-level statements:

```csharp
using System;

Console.WriteLine($"Argument count: {args.Length}");
```

### Pattern matching enhancements: relational patterns and logical patterns

- In C# 9.0, pattern matching capabilities were enhanced with the addition of relational patterns and logical patterns. Relational patterns allow the code to express conditions such as checking if a value falls within a certain range directly within a pattern, using operators like <, <=, >, and >=. For example, you could easily check if a numerical value is between two constants. Logical patterns introduce the ability to combine patterns with logical operators such as and, or, and not, enabling more complex condition checks within a single pattern expression. This means you can succinctly express conditions that require the combination or negation of patterns, making the code both clearer and more concise. These enhancements significantly improve the expressiveness and power of pattern matching in C#, allowing for more readable and maintainable code when dealing with complex conditional logic.

- **Relational Patterns Example**

- Relational patterns in C# 9.0 allow for comparing the relationship between an expression's value and a constant. This enhances the readability and conciseness of the code.

- Scenario: Temperature Categorization

```csharp
public static string CategorizeTemperature(double temperature) => temperature switch
{
    < 0 => "Freezing",
    >= 0 and < 15 => "Cold",
    >= 15 and < 25 => "Mild",
    >= 25 and < 30 => "Warm",
    >= 30 => "Hot",
    _ => "Unknown"
};

Console.WriteLine(CategorizeTemperature(-5));  // Output: Freezing
Console.WriteLine(CategorizeTemperature(10));  // Output: Cold
Console.WriteLine(CategorizeTemperature(20));  // Output: Mild
Console.WriteLine(CategorizeTemperature(27));  // Output: Warm
Console.WriteLine(CategorizeTemperature(35));  // Output: Hot
```

- **Logical Patterns Example**

- Logical patterns introduce the use of `and`, `or`, and `not` logical operators to combine multiple patterns into more complex conditions.

- Scenario: Special Offer Eligibility

```csharp
public record Person(int Age, bool IsMember);

public static bool IsEligibleForOffer(Person person) => person switch
{
    { Age: >= 18 and <= 65, IsMember: true } => true,
    { Age: >= 18 and <= 25, IsMember: false } => true,
    _ => false
};

var memberPerson = new Person(30, true);
var youngNonMemberPerson = new Person(22, false);
var seniorNonMemberPerson = new Person(70, false);

Console.WriteLine(IsEligibleForOffer(memberPerson));  // Output: True
Console.WriteLine(IsEligibleForOffer(youngNonMemberPerson));  // Output: True
Console.WriteLine(IsEligibleForOffer(seniorNonMemberPerson));  // Output: False
```

- These examples demonstrate the expressive and concise nature of relational and logical patterns in C# 9.0.

### Performance and interop

- C# 9.0 introduced enhancements in "Performance and Interop" aiming at supporting high-performance computing scenarios and better interoperability with unmanaged code. This includes:

- **Native sized integers (`nint` and `nuint`)**: These new types mirror the native size integers on the target machine, offering a more seamless interaction with unmanaged code without having to use `IntPtr` or `UIntPtr`. This makes it easier to work with pointer arithmetic and system calls requiring platform-specific integer sizes.

- **Function pointers**: Providing delegate-like functionality, function pointers allow for calling unmanaged functions directly, without the overhead of delegates. This is crucial for performance-critical applications that interact with native code, as it avoids the allocations associated with delegates.

- **Suppress emitting `localsinit` flag**: By allowing developers to omit the `localsinit` flag, C# 9.0 enables scenarios where the zero-initialization of locals can be skipped, potentially reducing the runtime overhead. This is particularly beneficial in performance-sensitive code paths where the initial values of locals are guaranteed to be set before use.

- These features collectively enhance C#'s capability for high-performance computing, making it more competitive for systems-level programming and closer in capability to languages traditionally considered more suitable for such tasks, like C and C++.

- **Using Native-Sized Integers for Performance**

- C# 9.0 introduces native-sized integers (`nint` and `nuint`) as built-in types to facilitate interoperability with unmanaged code and to write high-performance applications. These types are particularly useful when interacting with APIs requiring platform-specific pointer or integer sizes.

```csharp
// Demonstration of using native-sized integers to call a native method
[DllImport("SomeNativeLibrary")]
public static extern void PerformFastComputation(nint dataPtr, nuint length);

public void UseNativeIntegers()
{
    nint dataPtr = GetSomePointer();
    nuint length = GetSomeLength();

    PerformFastComputation(dataPtr, length);
}
```

- This example showcases how native-sized integers can be directly used in interop scenarios, enhancing the performance by eliminating the need for type conversion or marshaling when dealing with native library calls.

- **Improving Performance with Function Pointers**

- C# 9.0's introduction of function pointers brings C# closer to the performance of low-level languages by allowing direct invocation of methods in performance-critical paths without the overhead of delegates.

```csharp
using System;
// Defining a function pointer to a method with a specific signature
unsafe delegate*<int, int, int> addPtr = &Add;

int Add(int x, int y)
{
    return x + y;
}

public unsafe void UseFunctionPointer()
{
    int result = addPtr(2, 3);
    Console.WriteLine($"The result is {result}");
}
```

- This example illustrates using function pointers to directly invoke a method, bypassing the delegate's overhead. This is beneficial in high-performance computing scenarios where every bit of efficiency matters.

- **Module Initializers for Enhanced Startup Performance**

- C# 9.0 introduces module initializers, which are special methods executed when a module or assembly is loaded. This feature is useful for performing one-time initialization tasks, potentially improving the application's startup performance.

```csharp
using System;

class Program
{
    // Module initializer
    [ModuleInitializer]
    public static void Initialize()
    {
        Console.WriteLine("Module initializer executed.");
        PerformPreloadingTasks();
    }

    static void PerformPreloadingTasks()
    {
        // Preload necessary resources or perform initialization tasks
    }

    static void Main(string[] args)
    {
        Console.WriteLine("Main method executed.");
    }
}
```

- This example demonstrates a module initializer's use to perform initial setup tasks before any other code in the module runs, optimizing the application's startup sequence by preloading necessary resources.

### Native sized integers

- The new feature of "Native sized integers" in C# 9.0 offers a way to work with integers that are sized based on the native architecture of the CPU running the application. This means their size can be either 32 bits or 64 bits depending on whether the system is 32-bit or 64-bit. This enhancement is part of C# 9.0's broader push for performance and interop capabilities, particularly useful for scenarios requiring close interaction with the underlying hardware or calling native code, enabling more efficient handling of pointer and memory operations without the need for explicit size definitions in code. This addition supports high-performance computing scenarios by offering types nint and nuint, which model the native-size integer types on the target CPU, further enriching C#'s type system for modern applications and system-level programming.

- **Basic Operations with `nint` and `nuint`**

```csharp
// Example demonstrating basic arithmetic operations with nint and nuint
public class NativeIntegersExample
{
    public static void Main()
    {
        nint a = 10;
        nuint b = 20;
        nint c = a + (nint)b; // Explicit cast is needed for arithmetic operations between nint and nuint
        Console.WriteLine($"Sum: {c}");

        nint d = c - 5;
        Console.WriteLine($"Subtraction: {d}");

        nint e = d * 2;
        Console.WriteLine($"Multiplication: {e}");

        nint f = e / 2;
        Console.WriteLine($"Division: {f}");

        // Using nint for array indexing
        int[] numbers = new int[10];
        nint index = 5;
        numbers[index] = 99; // nint can be used as an array index
        Console.WriteLine($"Array at index {index}: {numbers[index]}");
    }
}
```

- **Comparison and Equality**

```csharp
// Example demonstrating comparison and equality operators with nint and nuint
public class ComparisonExample
{
    public static void Compare(nint a, nuint b)
    {
        Console.WriteLine($"a == b: {a == (nint)b}"); // Explicit cast is required for comparison
        Console.WriteLine($"a != b: {a != (nint)b}");
        Console.WriteLine($"a < b: {a < (nint)b}");
        Console.WriteLine($"a > b: {(nint)b > a}"); // Casting b to nint for comparison
    }
}
```

- **Working with Pointers**

```csharp
// Example demonstrating pointer arithmetic with nint and nuint
unsafe public class PointerArithmeticExample
{
    public static void Main()
    {
        int* numbers = stackalloc int[5];
        nint offset = 2;

        // Performing pointer arithmetic
        int* p = numbers + offset; // Implicit conversion of nint to long for pointer arithmetic
        *p = 99;

        Console.WriteLine($"Value at offset {offset}: {*p}");
    }
}
```

- **Interoperability with C APIs**

```csharp
// Example demonstrating the use of nint in P/Invoke
using System.Runtime.InteropServices;

public class NativeInteropExample
{
    // Assuming a C function: int32_t perform_operation(int32_t a, int32_t b);
    [DllImport("SomeCLibrary")]
    public static extern nint PerformOperation(nint a, nint b);

    public static void Main()
    {
        nint result = PerformOperation(10, 20);
        Console.WriteLine($"Result: {result}");
    }
}
```

### Function pointers

- In C# 9.0, a new feature called "Function pointers" was introduced. This enhancement allows for delegate-like functionality without the need to allocate memory for a delegate object, making it useful for high-performance computing scenarios. With function pointers, C# programs can now operate closer to the hardware level by directly invoking pointers to functions, similar to what is possible in languages like C and C++. This feature is part of a broader set of improvements aimed at supporting high-performance applications and interoperability with other languages and systems. It's designed for advanced scenarios where performance is critical, and it brings C# closer to low-level programming while maintaining the safety and productivity features the language is known for.

- **Using Function Pointers for Interop**

- Function pointers in C# 9.0 offer a way to define and use pointers to functions, similar to function pointers in languages like C++. They are particularly useful for interoperability scenarios, such as calling C libraries from C#.

```csharp
using System;
using System.Runtime.InteropServices;

class Program
{
    // Define a function pointer type that matches the signature of the C function you want to call.
    // For example, a function pointer for a C function with the signature: int add(int, int);
    [UnmanagedFunctionPointer(CallingConvention.Cdecl)]
    private delegate int AddDelegate(int a, int b);

    static void Main()
    {
        // Assume 'libmath' is a C library containing a function 'int add(int, int)'
        const string libraryPath = "libmath.so"; // Use appropriate file extension for Windows (.dll) or macOS (.dylib)

        // Load the library
        IntPtr libHandle = NativeLibrary.Load(libraryPath);

        // Get a pointer to the 'add' function
        IntPtr funcPtr = NativeLibrary.GetExport(libHandle, "add");

        // Convert the pointer to a delegate
        var addFunction = (AddDelegate)Marshal.GetDelegateForFunctionPointer(funcPtr, typeof(AddDelegate));

        // Now you can use 'addFunction' as if it were a regular delegate
        int result = addFunction(2, 3);
        Console.WriteLine($"2 + 3 = {result}");

        // Don't forget to free the library when you're done
        NativeLibrary.Free(libHandle);
    }
}
```

- **Performance-Oriented Scenarios**

- Function pointers are also advantageous for high-performance scenarios where avoiding delegate allocation overhead is crucial, such as in tight loops or performance-critical code paths.

```csharp
using System;
using System.Runtime.CompilerServices;

class PerformanceCritical
{
    // Define a function pointer using the 'delegate*'
    // Here, a function that takes two integers and returns an integer
    private unsafe delegate*<int, int, int> operation;

    public unsafe PerformanceCritical()
    {
        // Assign a function to the function pointer
        operation = &Add;
    }

    private static int Add(int a, int b) => a + b;

    public unsafe int ExecuteOperation(int a, int b)
    {
        // Use the function pointer
        return operation(a, b);
    }

    static unsafe void Main()
    {
        var pc = new PerformanceCritical();
        int result = pc.ExecuteOperation(5, 7);
        Console.WriteLine($"The result is: {result}");
    }
}
```

- In the first example, we see how function pointers can be used to interact with C libraries, enabling more straightforward interop scenarios without the need for P/Invoke or wrapping the functionality in C# delegates. The second example demonstrates using function pointers to improve the performance of your application by reducing the overhead associated with delegates, especially in critical code paths.

### Suppress emitting `localsinit` flag

- The "Suppress emitting `localsinit` flag" feature introduced in C# 9.0 allows developers to opt out of the automatic initialization of local variables to their default values by the Common Language Runtime (CLR). By default, the CLR initializes local variables in methods to ensure type safety. This process involves setting memory for stack-allocated variables (including those used with `stackalloc`) to zero. However, in performance-critical code, this automatic zero-initialization can be an unnecessary overhead, especially when variables are immediately set to specific values before use.

- To address this, C# 9.0 introduces the `SkipLocalsInitAttribute`, which can be applied to methods, classes, or even entire modules. When this attribute is present, the C# compiler does not emit the `localsinit` flag for the affected method(s), which instructs the JIT compiler to skip the zero-initialization of local variables. This can lead to performance improvements in certain scenarios, particularly where large stack-allocated arrays are used, and each element is assigned a value before any access occurs.

- This feature is specifically useful in high-performance computing scenarios where every cycle counts, and developers have full control over the initialization and usage of their variables. It's a compiler feature, not a language feature, meaning it affects how the code is compiled rather than introducing new syntax or constructs into the C# language itself .

- **Suppressing `localsinit` in a Method**

```csharp
using System;
using System.Runtime.CompilerServices;

public class HighPerformanceMethod
{
    [SkipLocalsInit]
    public static void PerformCalculation()
    {
        // Declare large structs
        Span<int> largeData = stackalloc int[512];

        // Perform operations without the overhead of locals init
        for (int i = 0; i < largeData.Length; i++)
        {
            largeData[i] = i * 2;
        }

        // Rest of the method
    }
}
```

- In this example, the `[SkipLocalsInit]` attribute is applied to `PerformCalculation`, indicating to the compiler not to emit the `localsinit` flag for this method. This means the local variables (`largeData` in this case) are not zero-initialized, reducing the overhead in this performance-critical section of the code.

- **Global Suppression**

- In some cases, you might want to apply this optimization to all methods within a class or even an entire assembly, to ensure that the `localsinit` flag is not emitted globally.

```csharp
using System;
using System.Runtime.CompilerServices;

[assembly: SkipLocalsInit]

public class HighPerformanceClass
{
    public void MethodOne()
    {
        Span<int> data = stackalloc int[256];
        // Method implementation
    }

    public void MethodTwo()
    {
        Span<byte> buffer = stackalloc byte[1024];
        // Method implementation
    }
}
```

- By placing the `[SkipLocalsInit]` attribute at the assembly level (using `[assembly: SkipLocalsInit]`), we ensure that no method within the assembly will have its local variables automatically zero-initialized, unless explicitly initialized in the code. This global approach is suitable for libraries or applications where performance is critical across all operations.

### Module initializers

- The new feature "Module Initializers" in C# 9.0 is a powerful addition that allows methods to be executed automatically when an assembly loads, without requiring explicit calls. This enhancement supports scenarios where code generators add functionality seamlessly. By designating specific methods as module initializers, developers ensure these methods run to initialize a module right as it becomes part of the application. This capability streamlines setups, especially in libraries or frameworks needing initial setup before use, enhancing automation and reducing boilerplate initialization code

- **Basic Module Initializer**

- In this example, we'll see how to define a simple module initializer that sets up some application-wide settings or performs necessary initialization steps before any type or method is accessed within the assembly.

```csharp
using System;
using System.Runtime.CompilerServices;

namespace ModuleInitializersDemo
{
    public class Settings
    {
        public static string ApplicationName { get; private set; }

        // Module Initializer
        [ModuleInitializer]
        public static void Initialize()
        {
            ApplicationName = "Module Initializers Demo Application";
            Console.WriteLine($"{ApplicationName} is initializing...");
            // Perform other initialization tasks here
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // Notice that the module initializer runs before this line.
            Console.WriteLine($"Welcome to {Settings.ApplicationName}");
        }
    }
}
```

- This example demonstrates a basic use case where the module initializer `Initialize` is automatically invoked when the assembly is loaded, even before the `Main` method execution begins. The `Initialize` method sets the application name and performs other initialization tasks.

- **Database Connection Initialization**

- Module initializers can also be useful for setting up database connections or reading configuration settings required across the application. Here's a simplified example:

```csharp
using System;
using System.Runtime.CompilerServices;

namespace ModuleInitializersDemo
{
    public static class DatabaseConfig
    {
        public static string ConnectionString { get; private set; }

        // Module Initializer for Database Configuration
        [ModuleInitializer]
        public static void InitializeDatabaseConnection()
        {
            // Assume fetching connection string from a config file or environment variable
            ConnectionString = "Server=myServerAddress;Database=myDataBase;User Id=myUsername;Password=myPassword;";
            Console.WriteLine("Database connection is initialized.");
        }
    }
}
```

- In this example, the module initializer `InitializeDatabaseConnection` sets up the database connection string for the application. This ensures that the database connection configuration is ready before any database operations are performed.

### New features for partial methods

- In C# 9.0, partial methods received a significant upgrade to work more effectively with code generators. Previously, partial methods could not have a return value, out parameters, or access modifiers, limiting their utility. However, with C# 9.0, these restrictions have been lifted, allowing partial methods to be almost indistinguishable from regular methods. They can now have return values, out parameters, and be declared with access modifiers like public. This enhancement means that partial methods can now be used in more scenarios, resembling the separation of header files and implementation seen in languages like C++. However, it's important to note that if a partial method is declared with an access modifier, its implementation must be provided; otherwise, the project won't compile. This feature is particularly useful for code generators and frameworks that rely on partial methods to allow developers to customize generated code.

- **Using Partial Methods with Return Value and Out Parameters**

- C# 9.0 extends the capabilities of partial methods, allowing them to have return values, out parameters, and access modifiers. This enhancement makes partial methods nearly as versatile as regular methods. Here's an example to illustrate the use of a partial method with a return value and an out parameter:

```csharp
public partial class Person
{
    public string Name { get; set; }
    public string Surname { get; set; }

    // Declaration of the partial method with an out parameter and a return value.
    public partial bool TrySpeak(string line, out string spokenText);
}

public partial class Person
{
    // Implementation of the partial method.
    public partial bool TrySpeak(string line, out string spokenText)
    {
        if (string.IsNullOrEmpty(line))
        {
            spokenText = string.Empty;
            return false;
        }

        spokenText = $"{Name} says: {line}";
        return true;
    }
}
```

- In this example, the `TrySpeak` partial method attempts to generate spoken text from a line. If the line is not empty or null, it constructs the spoken text and returns true; otherwise, it returns false.

- **Partial Method with Access Modifiers**

- Prior to C# 9.0, partial methods were implicitly private and did not allow specifying access modifiers. C# 9.0 removes this restriction, enabling developers to explicitly define the accessibility of partial methods. The following example shows how a public partial method can be declared and implemented:

```csharp
public partial class Calculator
{
    // Public partial method declaration.
    public partial int Add(int x, int y);
}

public partial class Calculator
{
    // Public partial method implementation.
    public partial int Add(int x, int y)
    {
        return x + y;
    }
}
```

- Here, the `Add` method is a public partial method that adds two integers.

- **Observations and Best Practices**

- The introduction of return values, out parameters, and access modifiers for partial methods in C# 9.0 enhances the utility of partial methods, especially in scenarios involving code generation or when dividing method implementation across multiple files.
- It's essential to ensure that if a partial method is declared with an access modifier or has a return value, its implementation must also be provided. Otherwise, the compiler will throw an error, enforcing the rule that such partial methods must be fully implemented.
- This feature facilitates a cleaner and more organized codebase, allowing for a separation of method signatures from their implementations. It can be particularly beneficial in large projects or when working with automatically generated code.

### Target-typed new expressions

- In C# 9.0, "Target-typed new expressions" is a new feature that simplifies how objects are instantiated. Previously, when creating an object, you had to specify the type both on the left and right sides of the assignment. This feature allows you to omit the type on the right side if the type is already known from the context. It makes code cleaner, especially when the type name is long or when initializing properties or collections. This enhancement is part of C# 9.0's broader efforts to reduce unnecessary coding "ceremony" and make code more concise and readable.

- ** Instantiating an Object**

- Before C# 9.0, specifying the type of the object explicitly was required when instantiating objects. This could sometimes lead to redundant code.

```csharp
// Before C# 9.0
Person person = new Person("John Doe");

// With C# 9.0 - Target-typed new expressions
Person person = new("John Doe");
```

_Comments:_ The type of `person` is clear from the declaration, making `new Person` redundant and allowing for a more concise `new` expression.

- **Passing New Instances as Method Arguments**

- You no longer need to explicitly specify the type when passing a new instance of an object as a method argument if the type can be inferred from the method's parameters.

```csharp
public void ProcessPerson(Person person) { /*...*/ }

// Before C# 9.0
ProcessPerson(new Person("John Doe"));

// With C# 9.0
ProcessPerson(new("John Doe"));
```

_Comments:_ The method `ProcessPerson` expects a `Person` object, allowing for a concise syntax when passing a new instance.

- **Collections Initialization**

- Initialization of collections with new objects is more succinct, improving readability.

```csharp
// Before C# 9.0
var people = new List<Person> {
    new Person("John Doe"),
    new Person("Jane Doe")
};

// With C# 9.0
var people = new List<Person> {
    new("John Doe"),
    new("Jane Doe")
};
```

_Comments:_ The type is inferred from `List<Person>`, simplifying the initialization of each `Person` in the list.

- **Dictionary Initialization**

- Dictionary initialization benefits from a concise syntax where the key and value types are known.

```csharp
// Before C# 9.0
var personAges = new Dictionary<string, int> {
    { "John Doe", 30 },
    { "Jane Doe", 28 }
};

// With C# 9.0
var personAges = new Dictionary<string, int> {
    ["John Doe"] = 30,
    ["Jane Doe"] = 28
};
```

_Comments:_ This example focuses on dictionary initializers, showcasing the readability improvements in C# 9.0 for initializing collections and objects.

### Static anonymous functions

- The new feature of "Static anonymous functions" in C# 9.0 enhances performance and prevents capturing local variables unintentionally. In previous versions, anonymous functions could capture local variables from their enclosing scope, potentially leading to unintentional memory usage and longer lifetimes for those variables. The "static" keyword now can be applied to anonymous functions, indicating that the function does not capture any local variables. This results in more efficient code, especially in performance-critical applications, as it reduces heap allocations and simplifies garbage collection by avoiding closures over the local state.

- **Basic Static Anonymous Function**

```csharp
// Using a static anonymous function to filter an array of numbers without capturing any external variables
int[] numbers = { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(static number => number % 2 == 0).ToArray();
```

- **Static Anonymous Function with Local Function**

```csharp
// Demonstrating how a static anonymous function can be used alongside a local function to perform a calculation
void ProcessNumbers(int[] nums)
{
    var result = nums.Select(static n => MultiplyByTwo(n)).ToArray();

    // Local function that multiplies a number by two
    static int MultiplyByTwo(int number) => number * 2;
}
```

- **Comparing Static and Non-Static Anonymous Functions**

```csharp
// Showing the difference in capturing local variables between static and non-static anonymous functions
int factor = 10;
Func<int, int> multiplyByFactorNonStatic = n => n * factor; // Captures the 'factor' variable from the enclosing scope

Func<int, int> multiplyByFactorStatic = static n => n * 10; // Does not capture any variables from the enclosing scope. Uses a constant instead.
```

- **Using a Static Anonymous Function in an Event Handler**

```csharp
// An example where a static anonymous function is used to handle an event without capturing any external variables
button.Click += static (sender, args) =>
{
    Console.WriteLine("Button clicked!");
};
```

_These examples illustrate various use cases for static anonymous functions in C#, highlighting their advantages in certain scenarios, especially when it comes to improving performance by avoiding the capture of variables from the enclosing scope._

### Target-typed conditional expressions

- The enhancement of "Target-typed conditional expressions" in C# 9.0 allows for more concise and readable code when working with conditional expressions. In earlier versions of C#, you might have needed explicit casting in certain conditional expressions, which could make the code longer and harder to read. With C# 9.0, the compiler is smarter in understanding the context of a conditional expression, reducing the need for such casts.

- For example, previously, when you had a conditional operation where both outcomes were different types but compatible with the context's expected type, you would need to explicitly cast one or both outcomes to the expected type. C# 9.0 eliminates this need in many cases by inferring the type based on the target variable or context, making your code shorter and cleaner.

- This feature is particularly useful in scenarios where you are working with nullable value types or when the outcome of your conditional expression depends on a condition that returns different types that are implicitly convertible to a common type. The compiler's ability to infer the target type simplifies the syntax, enhancing code readability and maintainability.

- **Null Coalescing in Object Initialization**

- This example demonstrates how target-typed conditional expressions simplify object initialization, especially when dealing with nullable types or providing fallback values.

```csharp
// Define a class with nullable properties
public class UserProfile
{
    public string? FirstName { get; init; }
    public string? LastName { get; init; }
}

// Assume we have a method that might return null for some properties
(string? firstName, string? lastName) GetUserDetailsFromDatabase()
{
    // Simulate database access
    return (null, "Smith"); // FirstName is null in this case
}

// Using target-typed conditional expression for object initialization
var (firstName, lastName) = GetUserDetailsFromDatabase();
var profile = new UserProfile
{
    // Use target-typed conditional expression to provide a fallback value
    FirstName = firstName ?? "Guest",
    LastName = lastName // LastName is directly assigned
};

Console.WriteLine($"Hello, {profile.FirstName} {profile.LastName}");
```

- Here, the target-typed conditional expression `firstName ?? "Guest"` provides a way to elegantly handle nullable values during object initialization, ensuring `UserProfile` always has a valid `FirstName`.

- **Conditional Operator in Method Arguments**

- This example illustrates using a target-typed conditional expression when calling a method, enabling dynamic argument selection based on a condition.

```csharp
public class Calculator
{
    // A method that requires a non-nullable integer
    public static int Add(int a, int b) => a + b;
}

// Conditional expression to decide which values to add
int userScore = GetUserScore(); // Assume this gets a user's score
int bonus = GetBonus(); // Assume this gets a bonus value

// Use target-typed conditional to dynamically choose the bonus based on the user's score
int totalScore = Calculator.Add(userScore, userScore >= 100 ? bonus : 0);

Console.WriteLine($"Total Score: {totalScore}");
```

- The expression `userScore >= 100 ? bonus : 0` decides at runtime whether to apply the bonus to the user's score, demonstrating a flexible way to pass arguments based on conditions.

- **Inline Variable Initialization with Conditional Operator**

- This example showcases inline variable initialization using a target-typed conditional expression to choose between two different types of objects based on a runtime condition.

```csharp
public interface IWidget
{
    void Display();
}

public class TextWidget : IWidget
{
    public void Display() => Console.WriteLine("Displaying TextWidget");
}

public class ImageWidget : IWidget
{
    public void Display() => Console.WriteLine("Displaying ImageWidget");
}

// Condition to determine widget type
bool isTextPreferred = GetUserPreference(); // Assume this checks the user's preference

// Use target-typed conditional to instantiate the appropriate widget
IWidget widget = isTextPreferred ? new TextWidget() : new ImageWidget() as IWidget;

widget.Display();
```

- In this scenario, `isTextPreferred ? new TextWidget() : new ImageWidget() as IWidget` chooses between `TextWidget` and `ImageWidget` based on the user's preference, illustrating how to use the feature for flexible object instantiation.

### Covariant return types

- In C# 9.0, the concept of covariant return types was introduced to allow an override method in a derived class to return a type that is more derived than the return type of the method in the base class. This enhancement makes the type system more flexible, enabling scenarios where the derived class methods can return types that are more specific than those defined in their base class counterparts, without compromising type safety or requiring casts. This feature is particularly useful in cases where a method in a base class returns an instance of itself, and the derived class needs to return an instance of the derived type, ensuring that the method's return type is consistent with the object's type.

- **Basic Usage of Covariant Return Types**

```csharp
// Base class with a method that returns an instance of 'Animal'
public class Zoo
{
    public virtual Animal GetAnimal()
    {
        return new Animal();
    }
}

// Derived class that overrides 'GetAnimal' to return a 'Lion', a subtype of 'Animal'
public class Safari : Zoo
{
    public override Lion GetAnimal()
    {
        return new Lion();
    }
}

public class Animal { }
public class Lion : Animal { }

// Usage
var safari = new Safari();
Lion lion = safari.GetAnimal(); // Directly assigns the returned 'Lion' without casting
```

- In this example, `Safari` class overrides the `GetAnimal` method from `Zoo` class. Thanks to covariant return types, it specifies that it returns a `Lion`, which is a more specific type than `Animal`. Clients of `Safari` can now directly work with a `Lion` without needing to cast the result from `Animal`.

- **Real-world Scenario with File Handling**

- Consider a scenario in a file handling system where a base class provides a method to get a file handler, and the derived class needs to return a more specific file handler type.

```csharp
public class FileHandler
{
    public virtual File OpenFile(string path)
    {
        // Logic to open a file and return a basic file handler
        return new File();
    }
}

public class EncryptedFileHandler : FileHandler
{
    public override EncryptedFile OpenFile(string path)
    {
        // Logic to open an encrypted file and return an encrypted file handler
        return new EncryptedFile();
    }
}

public class File { }
public class EncryptedFile : File { }

// Usage
var handler = new EncryptedFileHandler();
EncryptedFile file = handler.OpenFile("path/to/encryptedfile"); // Directly get an EncryptedFile
```

- In this example, `EncryptedFileHandler` extends `FileHandler`, providing an overridden `OpenFile` method that returns `EncryptedFile`, a more specific type than `File`. This makes it clear for the users of `EncryptedFileHandler` that they're dealing with encrypted files, providing a richer API experience without casting.

### Extension GetEnumerator support for foreach loops

- The "Extension GetEnumerator support for foreach loops" feature in C# 9.0 introduces a way for developers to iterate over collections more flexibly. In earlier versions of C#, to use a collection in a foreach loop, that collection needed to directly implement the IEnumerable interface. This feature extends that capability by allowing developers to use extension methods to define how a foreach loop iterates through any type, even if that type does not directly implement IEnumerable.

- This means you can create a custom iteration method for types that weren't originally designed to be used in a foreach loop. For example, if you have a custom data structure not inheriting from IEnumerable, you can now write an extension method that provides the GetEnumerator method, making it usable with foreach. This enhancement simplifies code and enhances reusability by allowing more types to integrate seamlessly with foreach loops, making iterating over custom data structures or third-party types more straightforward, without needing to modify their source code or wrap them in a collection type.

- **Using Extension `GetEnumerator` with a Custom Collection**

```csharp
using System;
using System.Collections;

// Custom collection class
public class MyCollection
{
    private int[] items = { 1, 2, 3, 4, 5 };

    // This method is not required by IEnumerable or IEnumerable<T>
    // Making use of the extension GetEnumerator method for foreach loops
    public IEnumerator GetEnumerator()
    {
        foreach (var item in items)
        {
            yield return item; // Yielding items one by one
        }
    }
}

// Extension method class
public static class EnumerableExtensions
{
    // Extension GetEnumerator method to support foreach loops on MyCollection
    public static IEnumerator GetEnumerator(this MyCollection collection)
    {
        // Calling the original GetEnumerator of MyCollection
        return collection.GetEnumerator();
    }
}

class Program
{
    static void Main(string[] args)
    {
        var myCollection = new MyCollection();

        // Using foreach loop with MyCollection thanks to the extension GetEnumerator method
        foreach (var item in myCollection)
        {
            Console.WriteLine(item); // Outputs: 1 2 3 4 5
        }
    }
}
```

- **Iterating Over a Tuple with Extension `GetEnumerator`**

```csharp
using System;
using System.Collections.Generic;

// Extension method class
public static class TupleExtensions
{
    // Extension GetEnumerator method to support foreach loops over a Tuple
    public static IEnumerator<T> GetEnumerator<T>(this (T, T, T) tuple)
    {
        yield return tuple.Item1; // Yielding tuple items one by one
        yield return tuple.Item2;
        yield return tuple.Item3;
    }
}

class Program
{
    static void Main()
    {
        var myTuple = (1, 2, 3);

        // Using foreach loop with a Tuple<int, int, int> thanks to the extension GetEnumerator method
        foreach (var item in myTuple)
        {
            Console.WriteLine(item); // Outputs: 1 2 3
        }
    }
}
```

### Lambda discard parameters

- The new "Lambda discard parameters" feature in C# 9.0 simplifies lambda expressions by allowing the use of underscores (\_) to signify parameters that are not used within the lambda's body. This means when you have a lambda expression where you don't need to use one or more of the parameters provided to it, you can replace those parameters with an underscore. This not only makes your code cleaner and easier to understand but also reduces the need for naming unused parameters. It's particularly useful in scenarios where you're working with higher-order functions or LINQ queries that require lambda expressions, but some parameters are irrelevant to the logic you're implementing.

- **Filtering a List**

- Suppose you have a list of numbers, and you want to filter out only the even numbers using a lambda expression. You don't need the index provided by the `Select` method, so you can discard it.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main()
    {
        var numbers = new List<int> { 1, 2, 3, 4, 5, 6 };

        // Using a lambda discard parameter for the index (_), which we don't need.
        var evenNumbers = numbers.Select((number, _) => number).Where(n => n % 2 == 0).ToList();

        Console.WriteLine(string.Join(", ", evenNumbers));
        // Output: 2, 4, 6
    }
}
```

- In the above example, `_` is used to discard the index parameter of the `Select` method since it's not used in the lambda expression.

- **Event Handling**

- Imagine you have a button in a GUI application, and you want to handle its click event. Often, you don't need the `sender` and `eventArgs` parameters provided by the event handler.

```csharp
using System;

class Button
{
    public event EventHandler Clicked;
    public void Click()
    {
        Clicked?.Invoke(this, EventArgs.Empty);
    }
}

class Program
{
    static void Main()
    {
        var button = new Button();

        // Using lambda discard parameters for both sender and eventArgs.
        button.Clicked += (_, _) => Console.WriteLine("Button was clicked.");

        button.Click();
        // Output: Button was clicked.
    }
}
```

- Here, both parameters in the lambda expression are discarded because they are not needed to handle the click event.

- **Exception Handling**

- Sometimes, you might want to catch an exception but you're not interested in the exception object itself.

```csharp
using System;

class Program
{
    static void Main()
    {
        try
        {
            // Some operation that can fail
            throw new InvalidOperationException("An error occurred.");
        }
        catch (InvalidOperationException _)
        {
            // Handling the exception without using the exception object
            Console.WriteLine("An operation failed.");
        }
    }
}
```

- In this case, the `_` is used to discard the `InvalidOperationException` object, as we only want to print a generic error message.

### Attributes on local functions

- In C# 9.0, a noteworthy enhancement is the introduction of "Attributes on local functions." This feature elevates the functionality of local functions, allowing developers to adorn them with attributes, a capability previously reserved for more prominent class-level methods. This augmentation opens the door to more refined control and adaptability in coding practices, enhancing the annotation capabilities directly within the scope of local functions. It's a concise yet potent enhancement, streamlining the coding process and expanding the versatility of local functions within the C# language.

- **Using a Custom Attribute on a Local Function**

```csharp
using System;

public class Program
{
    // Custom attribute definition
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = false)]
    public class MyCustomAttribute : Attribute { }

    public static void Main(string[] args)
    {
        // Local function with custom attribute
        [MyCustomAttribute]
        void LocalFunctionWithAttribute()
        {
            Console.WriteLine("This local function has a custom attribute.");
        }

        LocalFunctionWithAttribute();
    }
}
```

- This example defines a custom attribute `MyCustomAttribute` and applies it to a local function `LocalFunctionWithAttribute` within the `Main` method. The attribute does not alter the behavior of the function in this example but serves as metadata that can be inspected through reflection or used by custom tools or libraries.

- **Using the `ConditionalAttribute` on a Local Function**

```csharp
#define DEBUG
using System;
using System.Diagnostics;

public class Program
{
    public static void Main(string[] args)
    {
        // Local function with ConditionalAttribute
        [Conditional("DEBUG")]
        void DebugPrint(string message)
        {
            Console.WriteLine($"DEBUG: {message}");
        }

        DebugPrint("Starting application...");

        // Main method implementation
        Console.WriteLine("Hello, World!");

        DebugPrint("Application is running.");
    }
}
```

- In this example, the `ConditionalAttribute` is applied to a local function `DebugPrint`. This attribute causes the invocation of the `DebugPrint` function to be included or omitted based on the `DEBUG` symbol definition. If the `DEBUG` symbol is defined (as it is in this example), calls to `DebugPrint` are included and executed. If the `DEBUG` symbol is not defined, calls to `DebugPrint` are omitted by the compiler, effectively removing debug print statements from the release builds.

## Conclusion

- C# 9.0 introduces a wealth of new features aimed at enhancing the clarity, simplicity, and expressiveness of code. With the introduction of records, developers are empowered to easily define immutable data models, promoting safer, more predictable programming practices. Init-only properties further support immutability, allowing objects to be fully initialized in a flexible yet controlled manner. The simplification provided by top-level statements and the depth added by pattern matching enhancements make C# more approachable to newcomers while offering seasoned developers powerful tools for code expression.

- Performance and interop features, such as native sized integers and function pointers, address high-performance computing needs, enabling more efficient operations and interaction with unmanaged code. Additionally, language enhancements like target-typed new expressions and static anonymous functions streamline coding patterns and improve performance.

- In conclusion, C# 9.0 significantly enriches the language, making it more versatile and developer-friendly. These improvements continue to position C# as a leading choice for modern software development, catering to a broad spectrum of programming paradigms and applications. By embracing these new features, developers can leverage the full potential of C# to build robust, efficient, and maintainable software solutions.
