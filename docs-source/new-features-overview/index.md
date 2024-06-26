# C# New Features Overview

## Table of Contents

1. [C# 6.0 New Features Overview](./csharp-6.0.md)
   - [Auto-Property Enhancements](./csharp-6.0.md#auto-property-enhancements)
   - [String Interpolation](./csharp-6.0.md#string-interpolation)
   - [`nameof` Expressions](./csharp-6.0.md#nameof-expressions)
   - [Null-conditional Operators](./csharp-6.0.md#null-conditional-operators)
   - [Exception Filters](./csharp-6.0.md#exception-filters)
   - [Expression-bodied Members](./csharp-6.0.md#expression-bodied-members)
   - [Await in Catch and Finally Blocks](./csharp-6.0.md#await-in-catch-and-finally-blocks)
   - [Index Initializers](./csharp-6.0.md#index-initializers)
2. [C# 7.0 New Features Overview](./csharp-7.0.md)
   - [Tuples and Deconstruction](./csharp-7.0.md#tuples-and-deconstruction)
   - [Pattern Matching](./csharp-7.0.md#pattern-matching)
   - [Local Functions](./csharp-7.0.md#local-functions)
   - [Ref Returns and Locals](./csharp-7.0.md#ref-returns-and-locals)
   - [Out Variables](./csharp-7.0.md#out-variables)
   - [Literal Improvements](./csharp-7.0.md#literal-improvements)
   - [Generalized Async Return Types](./csharp-7.0.md#generalized-async-return-types)
   - [Expression Bodied Members Expansion](./csharp-7.0.md#expression-bodied-members-expansion)
   - [Throw Expressions](./csharp-7.0.md#throw-expressions)
3. [C# 7.1 New Features Overview](./csharp-7.1.md)
   - [Tuple Projection Initializers](./csharp-7.1.md#tuple-projection-initializers)
   - [Pattern Matching with Generics](./csharp-7.1.md#pattern-matching-with-generics)
   - [Default Expression](./csharp-7.1.md#default-expression)
   - [Async Main](./csharp-7.1.md#async-main)
4. [C# 7.2 New Features Overview](./csharp-7.2.md)
   - [Leading Underscores in Numeric Literals](./csharp-7.2.md#leading-underscores-in-numeric-literals)
   - [Non-trailing Named Arguments](./csharp-7.2.md#non-trailing-named-arguments)
   - [Contiguous Memory Accessor for Stack-Allocated Structures `Span<T>`](./csharp-7.2.md#contiguous-memory-accessor-for-stack-allocated-structures-spant)
   - [Private Protected Access Modifier](./csharp-7.2.md#private-protected-access-modifier)
   - [Ref Conditional Expression](./csharp-7.2.md#ref-conditional-expression)
   - [Ref extension methods on structs](./csharp-7.2.md#ref-extension-methods-on-structs)
   - [Readonly Ref](./csharp-7.2.md#readonly-ref)
5. [C# 7.3 New Features Overview](./csharp-7.3.md)
   - [Enhanced Access to Fixed Fields](./csharp-7.3.md#enhanced-access-to-fixed-fields)
   - [Flexible Ref Local Variables](./csharp-7.3.md#flexible-ref-local-variables)
   - [Stackalloc Array Initializers](./csharp-7.3.md#stackalloc-array-initializers)
   - [Extended Fixed Statement Usability](./csharp-7.3.md#extended-fixed-statement-usability)
   - [Expanded Generic Constraints](./csharp-7.3.md#expanded-generic-constraints)
   - [Tuple Equality Operations](./csharp-7.3.md#tuple-equality-operations)
   - [Broader Expression Variable Scope](./csharp-7.3.md#broader-expression-variable-scope)
   - [Attributes on Auto-Implemented Property Backing Fields](./csharp-7.3.md#attributes-on-auto-implemented-property-backing-fields)
   - [Refined Method Resolution Logic](./csharp-7.3.md#refined-method-resolution-logic)
6. [C# 8.0 New Features Overview](./csharp-8.0.md)
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
7. [C# 9.0 New Features Overview](./csharp-9.0.md)
   - [Records](./csharp-9.0.md#records)
   - [Init-only properties](./csharp-9.0.md#init-only-properties)
   - [Top-level statements](./csharp-9.0.md#top-level-statements)
   - [Pattern matching enhancements: relational patterns and logical patterns](./csharp-9.0.md#pattern-matching-enhancements-relational-patterns-and-logical-patterns)
   - [Performance and interop](./csharp-9.0.md#performance-and-interop)
   - [Native sized integers](./csharp-9.0.md#native-sized-integers)
   - [Function pointers](./csharp-9.0.md#function-pointers)
   - [Suppress emitting `localsinit` flag](./csharp-9.0.md#suppress-emitting-localsinit-flag)
   - [Module initializers](./csharp-9.0.md#module-initializers)
   - [New features for partial methods](./csharp-9.0.md#new-features-for-partial-methods)
   - [Target-typed new expressions](./csharp-9.0.md#target-typed-new-expressions)
   - [Static anonymous functions](./csharp-9.0.md#static-anonymous-functions)
   - [Target-typed conditional expressions](./csharp-9.0.md#target-typed-conditional-expressions)
   - [Covariant return types](./csharp-9.0.md#covariant-return-types)
   - [Extension GetEnumerator support for foreach loops](./csharp-9.0.md#extension-getenumerator-support-for-foreach-loops)
   - [Lambda discard parameters](./csharp-9.0.md#lambda-discard-parameters)
   - [Attributes on local functions](./csharp-9.0.md#attributes-on-local-functions)
8. [C# 10.0 New Features Overview](./csharp-10.0.md)
   - [Record structs](./csharp-10.0.md#record-structs)
   - [Improvements of structure types](./csharp-10.0.md#improvements-of-structure-types)
   - [Interpolated string handlers](./csharp-10.0.md#interpolated-string-handlers)
   - [Global using directives](./csharp-10.0.md#global-using-directives)
   - [File-scoped namespace declaration](./csharp-10.0.md#file-scoped-namespace-declaration)
   - [Extended property patterns](./csharp-10.0.md#extended-property-patterns)
   - [Improvements on lambda expressions](./csharp-10.0.md#improvements-on-lambda-expressions)
   - [Allow const interpolated strings](./csharp-10.0.md#allow-const-interpolated-strings)
   - [Record types can seal ToString()](./csharp-10.0.md#record-types-can-seal-tostring)
   - [Improved definite assignment](./csharp-10.0.md#improved-definite-assignment)
   - [Allow both assignment and declaration in the same deconstruction](./csharp-10.0.md#allow-both-assignment-and-declaration-in-the-same-deconstruction)
   - [Allow AsyncMethodBuilder attribute on methods](./csharp-10.0.md#allow-asyncmethodbuilder-attribute-on-methods)
   - [CallerArgumentExpression attribute](./csharp-10.0.md#callerargumentexpression-attribute)
   - [Enhanced #line pragma](./csharp-10.0.md#enhanced-line-pragma)
   - [Warning wave 6](./csharp-10.0.md#warning-wave-6)
9. [C# 11.0 New Features Overview](./csharp-11.0.md)
   - [Generic attributes](./csharp-11.0.md#generic-attributes)
   - [Generic math support](./csharp-11.0.md#generic-math-support)
   - [Numeric IntPtr and UIntPtr](./csharp-11.0.md#numeric-intptr-and-uintptr)
   - [Newlines in string interpolations](./csharp-11.0.md#newlines-in-string-interpolations)
   - [Raw string literals](./csharp-11.0.md#raw-string-literals)
   - [UTF-8 string literals](./csharp-11.0.md#utf-8-string-literals)
   - [List patterns](./csharp-11.0.md#list-patterns)
   - [File-local types](./csharp-11.0.md#file-local-types)
   - [Required members](./csharp-11.0.md#required-members)
   - [Auto-default structs](./csharp-11.0.md#auto-default-structs)
   - [Pattern match `Span<char>` on a constant string](./csharp-11.0.md#pattern-match-spanchar-on-a-constant-string)
   - [Extended `nameof` scope](./csharp-11.0.md#extended-nameof-scope)
   - [`ref` fields and `scoped ref`](./csharp-11.0.md#ref-fields-and-scoped-ref)
   - [Improved method group conversion to delegate](./csharp-11.0.md#improved-method-group-conversion-to-delegate)
   - [Warning wave 7](./csharp-11.0.md#warning-wave-7)
10. [C# 12.0 New Features Overview](./csharp-12.0.md)
    - [Primary Constructors](./csharp-12.0.md#primary-constructors)
    - [Inline Arrays](./csharp-12.0.md#inline-arrays)
    - [Default Lambda Parameters](./csharp-12.0.md#default-lambda-parameters)
    - [ref readonly Parameters](./csharp-12.0.md#ref-readonly-parameters)
    - [Alias Any Type with using](./csharp-12.0.md#alias-any-type-with-using)
    - [Experimental Attribute](./csharp-12.0.md#experimental-attribute)
    - [Interceptors](./csharp-12.0.md#interceptors)
    - [Collection Expressions](./csharp-12.0.md#collection-expressions)

[Previous: Introduction](../introduction.md) | [Back to main page](../../README.md) | [Next: C# 6.0 New Features Overview](./csharp-6.0.md)
