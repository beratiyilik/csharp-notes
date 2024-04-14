# C# 7.1 New Features Overview

## Table of Contents

- [C# 7.1 New Features Overview](#c-71-new-features-overview)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [New Features](#new-features)
    - [Tuple Projection Initializers](#tuple-projection-initializers)
    - [Pattern Matching with Generics](#pattern-matching-with-generics)
    - [Default Expression](#default-expression)
    - [Async Main](#async-main)
  - [Conclusion](#conclusion)

## Introduction

In this guide, we explore C# 7.1's new features that make coding easier and more efficient. Learn about Tuple Projection Initializers for simpler tuple usage, Pattern Matching with Generics for flexible data handling, Default Expression for effortless defaults, and Async Main for direct asynchronous operations in the main method.

## New Features

### Tuple Projection Initializers

In C# 7.1, a new feature called Tuple Projection Initializers was introduced. This feature makes it easier to work with tuples, which are a way to store a group of values together. Before this update, if you wanted to create a tuple, you had to specify the names of the elements inside the tuple. This could be a bit repetitive because you might end up writing the same name twice.

With Tuple Projection Initializers, C# can now understand and use the names of variables you are using to create a tuple without you having to repeat them. So, if you have a variable named `age` and another named `name`, and you want to create a tuple with these two, you don't have to say `(name: name, age: age)` anymore. Instead, you can just write `(name, age)`, and C# automatically knows that the first element should be named `name` and the second `age`. This makes writing code faster and less repetitive, making your work a little easier!

- **Old Way**

  ```csharp
    string firstName = "John";
    string lastName = "Doe";
    int age = 30;
    // Before C# 7.1
    var personBefore = (FirstName: firstName, LastName: lastName, Age: age);
  ```

- **New Way**

  ```csharp
    // C# 7.1 and later
    var personAfter = (firstName, lastName, age);
    Console.WriteLine($"Name: {personAfter.firstName} {personAfter.lastName}, Age: {personAfter.age}");
  ```

- **Comprehensive Example**

  ```csharp
    var people = new List<(string FirstName, string LastName, int Age)>
    {
        ("Jane", "Doe", 22),
        ("John", "Smith", 30),
        ("Anne", "Jones", 25)
    };
    // Using Tuple Projection Initializers in a LINQ query
    var youngPeople = from person in people
                  where person.Age < 30
                  select (person.FirstName, person.LastName);
    foreach (var p in youngPeople)
    {
        Console.WriteLine($"{p.FirstName} {p.LastName}");
    }
  ```

### Pattern Matching with Generics

In C# 7.1, there's a new feature that makes coding a bit easier and more versatile, called "Pattern Matching with Generics". Before C# 7.1, you could use pattern matching, which is a way to check the type or value of an object and act differently depending on what it is. However, you couldn't do this as easily with generic types, which are types that work with any data type, like a list or array that can hold any type of items.

With C# 7.1, you can now use pattern matching with these generic types too. This means if you have a function that works on different types of data, you can more smoothly check what type of data it's currently working with and handle it appropriately. This update makes your code more flexible and easier to manage, especially when working with a mix of types.

```csharp
    public abstract class Product
    {
        public string Name { get; set; }
        public decimal Price { get; set; }
    }

    public class Book : Product
    {
        public string Author { get; set; }
        public int Pages { get; set; }
    }

    public class Electronics : Product
    {
        public string Manufacturer { get; set; }
        public int WarrantyPeriodInMonths { get; set; }
    }

    public class Grocery : Product
    {
        public DateTime ExpiryDate { get; set; }
    }

    public static void ProcessProduct<T>(T product) where T : Product
    {
        switch (product)
        {
            case Book book:
                Console.WriteLine($"Processing a book: {book.Name}, by {book.Author}");
                // Apply a discount for books
                book.Price *= 0.9m;
                break;
            case Electronics electronics:
                Console.WriteLine($"Processing electronics: {electronics.Name}, by {electronics.Manufacturer}");
                // Calculate warranty expiration date
                DateTime warrantyEnd = DateTime.Now.AddMonths(electronics.WarrantyPeriodInMonths);
                Console.WriteLine($"Warranty expires on: {warrantyEnd.ToShortDateString()}");
                break;
            case Grocery grocery:
                Console.WriteLine($"Processing grocery item: {grocery.Name}, expires on {grocery.ExpiryDate.ToShortDateString()}");
                // Check if the grocery item is close to its expiry date
                if (grocery.ExpiryDate.AddDays(-7) <= DateTime.Now)
                {
                    Console.WriteLine("This item is close to expiry and needs to be sold soon!");
                }
                break;
            default:
                Console.WriteLine($"Processing a general product: {product.Name}");
                break;
        }
    }

    var myBook = new Book { Name = "The C# Programming Language", Price = 49.99m, Author = "Jon Skeet", Pages = 900 };
    var myElectronics = new Electronics { Name = "Laptop", Price = 999.99m, Manufacturer = "TechCo", WarrantyPeriodInMonths = 24 };
    var myGrocery = new Grocery { Name = "Apple", Price = 0.99m, ExpiryDate = DateTime.Now.AddDays(5) };

    ProcessProduct(myBook);
    ProcessProduct(myElectronics);
    ProcessProduct(myGrocery);
```

### Default Expression

In C# 7.1, the "Default Expression" feature was enhanced for simplicity and ease of use. Before this update, to set a variable to its default value, you needed to specify the type of the variable explicitly with `default(Type)`. However, C# 7.1 introduced a new way to use the `default` keyword without specifying the type. Now, you can simply write `default` on its own, and the compiler understands the type automatically from the context. This makes your code cleaner and reduces repetition, especially when dealing with complex types.

```csharp
// Before C# 7.1
int number1 = default(int);
string text1 = default(string);
bool flag1 = default(bool);

// C# 7.1 and later
int number2 = default;
string text2 = default;
bool flag2 = default;

Console.WriteLine($"Number: {number2}, Text: {text2}, Flag: {flag2}");

public void LogMessage(string message = default)
{
    if (message == null) message = "No message provided";
    Console.WriteLine(message);
}

public T CreateOrDefault<T>() where T : new()
{
    return default(T);  // In C# 7.1 and later, you can simply write return default;
}

int? GetNullableInt(bool returnNull)
{
    return returnNull ? default : 42;  // Automatically inferred as default(int?)
}

public int? GetIdOrNull(bool hasId)
{
    return hasId ? 123 : default;  // Equivalent to default(int?)
}

public string DescribeShape(Shape shape)
{
    switch (shape)
    {
        case Circle c:
            return "circle";
        case Rectangle r:
            return "rectangle";
        default:
            return "unknown shape";
    }
}

public Dictionary<TKey, TValue> CreateDictionary<TKey, TValue>()
{
    return default;  // Equivalent to default(Dictionary<TKey, TValue>), which is null
}
```

### Async Main

In C# 7.1, a new feature called "Async Main" allows programmers to use asynchronous operations directly within the main entry point of their programs. Before this update, the main method couldn't handle asynchronous tasks directly, making it tricky to perform operations that require waiting, like web requests or file reading, without extra work. Now, with "Async Main," programmers can simplify their code, making it more straightforward to execute tasks that might take some time to complete, directly from the start of their applications. This improvement helps in creating cleaner and more efficient programs, especially when dealing with operations that rely on waiting for resources or processes to complete.

- This example demonstrates a basic use case where we asynchronously wait for a task to complete using `Task.Delay` to simulate an asynchronous operation, such as fetching data from an API or a database.

  ```csharp
    using System;
    using System.Threading.Tasks;

    class Program
    {
       static async Task Main(string[] args)
        {
            Console.WriteLine("Async operation started.");
            await Task.Delay(2000); // Simulate an asynchronous operation
            Console.WriteLine("Async operation completed.");
        }
    }
  ```

- In this example, we simulate fetching data asynchronously and processing it. This could be fetching data from a web service or reading a file asynchronously.

  ```csharp
  using System;
  using System.Threading.Tasks;

  class Program
  {
      static async Task Main(string[] args)
      {
          string data = await FetchDataAsync();
          Console.WriteLine($"Data fetched: {data}");
      }

      static async Task<string> FetchDataAsync()
      {
          // Simulate an asynchronous operation to fetch data
          await Task.Delay(1000);
          return "Sample Data";
      }
  }
  ```

- C# 7.1 allows the `Main` method to return a `Task<int>` as well, enabling you to return an exit code from an asynchronous `Main` method. This is useful for console applications that need to signal success or error states to the operating system or calling process.

  ```csharp
  using System;
  using System.Threading.Tasks;

  class Program
  {
      static async Task<int> Main(string[] args)
      {
          try
          {
              await Task.Delay(1000); // Simulate an async operation
              Console.WriteLine("Operation completed successfully.");
              return 0; // Success
          }
          catch (Exception)
          {
              Console.WriteLine("An error occurred.");
              return 1; // Error
          }
      }
  }
  ```

- These examples illustrate the flexibility and simplicity that "Async Main" brings to C# console applications, allowing for straightforward execution of asynchronous code right from the entry point of the application.

## Conclusion

C# 7.1 brought significant enhancements to the language, making coding more intuitive and efficient. With features like Tuple Projection Initializers, Pattern Matching with Generics, the Default Expression, and Async Main, developers can write cleaner, more concise code. These improvements streamline development workflows, allowing for more focus on solving complex problems and less on boilerplate code. Whether you're managing data collections, performing asynchronous operations, or initializing variables, C# 7.1 offers tools to make these tasks more straightforward, showcasing the language's evolution toward greater productivity and simplicity.

[Previous: C# 7.0 New Features Overview](./csharp-7.0.md) | [Back to main page](../../README.md) | [Next: C# 7.2 New Features Overview](./csharp-7.2.md)
