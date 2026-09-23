---
title: "Evaluating Zig"
author: "Jim Carr"
date: "2025-08-16"
categories: [Zig]
draft: false
---

The results of the 2025 Stack Overflow Developer Survey were [recently published](https://survey.stackoverflow.co/2025/) and I noticed that [interest in the Zig programming language is rising](https://survey.stackoverflow.co/2025/technology#admired-and-desired-language-desire-admire).  I'd heard of Zig, but knew nothing about the details.

Let's check it out!

# What is Zig?

![](images/zig-logo.png)

A summary from the [Zig home page](https://ziglang.org) and other sources:

Zig is a general-purpose programming language that emphasizes **performance**, **safety**, and **maintainability**. It was created by Andrew Kelley and has gained traction for its ability to provide low-level control over system resources while maintaining a modern syntax and features that enhance developer productivity. Zig is particularly well-suited for systems programming, game development, and applications where performance is critical.

{{< video https://www.youtube.com/watch?v=Gv2I7qTux7g >}}

## Key Features of Zig

* **Manual Memory Management**: Zig allows developers to manage memory manually, similar to C, but with added safety features. This gives developers fine-grained control over memory allocation and deallocation, which is essential for performance-critical applications.
* **Error Handling**: Zig introduces a unique approach to error handling that avoids exceptions and instead uses a simple return value mechanism. This makes it easier to reason about error states and ensures that errors are handled explicitly.
* **Cross-Compilation**: Zig is designed with cross-compilation in mind, making it easy to build applications for different platforms from a single codebase. This is particularly useful for developers targeting multiple operating systems or architectures.
* **Interoperability with C**: Zig can directly call C functions and use C libraries without the need for wrappers or bindings. This makes it easy to integrate Zig into existing C projects or leverage existing C libraries.

## Why Choose Zig?

Zig stands out in a crowded field of programming languages for several reasons:

* **Performance**: With its low-level capabilities and manual memory management, Zig is designed for high-performance applications, making it an excellent choice for systems programming and game development.
* **Simplicity**: Zig's syntax is clean and straightforward, making it accessible for new developers while still providing the power needed for experienced programmers.
* **Safety**: The language's focus on safety features, such as compile-time checks and explicit error handling, helps prevent common programming errors that can lead to crashes or security vulnerabilities.
* **Active Community**: Zig has a growing community of developers who contribute to its development and provide support through forums, documentation, and tutorials.

# Installation (Direct Download)

1. Download the latest version from <https://ziglang.org/download/>
2. Extract and move to **~/bin/zig/**
3. Add Zig to the PATH in .profile: `export PATH=$PATH:~/bin/zig`

Test the compiler:

```bash
mkdir hello-world

cd hello-world

zig init

zig build run
```

Output:

```txt
All your codebase are belong to us.
Run `zig build test` to run the tests.
```

# VS Code

Install the [Zig Language extension](https://marketplace.visualstudio.com/items?itemName=ziglang.vscode-zig).  It includes a code runner, code debugger, and test runners.

:::{.callout-tip}
Install the extension (along with any other supporting extensions) inside a separate profile dedicated solely to Zig development.

If you aren't already using profiles in VS Code, I _highly_ recommend it.  Profiles make it easy to construct curated development environments and are also great for evaluating new extensions.
:::

# Get Started

## Hello!

Let's start with the simplest possible example:

```{.zig filename="hello.zig"}
const std = @import("std");

pub fn main() !void {
    const stdout = std.io.getStdOut().writer();

    try stdout.print("Hello, World!\n", .{});
}
```

Since this is a standalone source file (not a project), we use **build-exe** instead of **build**:

```bash
zig build-exe hello.zig
```

This produces two files:

* **hello.o** (object file), and
* **hello** (executable)

## Code Breakdown

### Importing the Standard Library

```zig
const std = @import("std");
```

This line imports the standard library of Zig, which provides various functionalities, including input/output operations, memory management, and more. The `std` constant will be used to access these functionalities.

### Main Function Definition

```zig
pub fn main() !void {
```

This line defines the `main` function, which is the entry point of the program. The `pub` keyword makes it public, allowing it to be called from outside the module. The `!void` return type indicates that the function can return an error (denoted by `!`) or nothing (`void`).

### Getting the Standard Output Writer

```zig
const stdout = std.io.getStdOut().writer();
```

Here, the program retrieves the standard output stream using `std.io.getStdOut()`. The `writer()` method is called on this output stream to get a writer object that can be used to print text to the console.

### Printing to Standard Output

```zig
try stdout.print("Hello, World!\n", .{});
```

This line attempts to print the string "Hello, World!" followed by a newline character (`\n`) to the standard output. The `try` keyword is used to handle any potential errors that may occur during the printing process. If an error occurs, it will propagate up to the caller of `main`.

# Performance and Safety

Zig has four [build modes](https://ziglang.org/documentation/master/#Build-Mode):

Parameter | Debug | ReleaseSafe | ReleaseFast | ReleaseSmall
--------- | ----- | ----------- | ----------- | ------------
Optimizations - improve speed, harm debugging, harm compile time | | On | On | On
Runtime Safety Checks - harm speed, harm size, crash instead of undefined behavior | On | On | | |

Regardless of the build mode, here's what an integer overflow looks like at compile time:

```{.zig filename="overflow.zig"}
pub fn main() !void {
    const x: u8 = 255;

    // The underscore indicates that the value is unused:
    _ = x + 1;
}
```

```bash
zig build-exe overflow.zig
```

```txt
overflow.zig:4:11: error: overflow of integer type 'u8' with value '256'
    _ = x + 1;
        ~~^~~
```

Zig also has a `test` keyword that can be used to define unit tests:

```{.zig filename="overflow.zig"}
test "integer overflow at compile time" {
    const x: u8 = 255;

    _ = x + 1;
}
```

With `zig test`, zig will locate all unit tests and execute them:

```bash
zig test overflow.zig 
```

```txt
overflow.zig:4:11: error: overflow of integer type 'u8' with value '256'
    _ = x + 1;
        ~~^~~
```

# Manual Memory Management

Manual memory management in Zig is a key feature that allows developers to have fine-grained control over memory allocation and deallocation. Unlike languages with automatic garbage collection, Zig requires programmers to explicitly manage memory.

Here's an example:

```{.zig filename="allocator.zig"}
const std = @import("std");

pub fn main() !void {
    const allocator = std.heap.page_allocator;

    var number = try allocator.alloc(i32, 1);

    number[0] = 42;

    std.debug.print("The value is: {}\n", .{number[0]});

    allocator.free(number);
}
```

Output:

```txt
The value is: 42
```

## Code Breakdown

### Importing Standard Library

```zig
const std = @import("std");
```

This line imports the standard library, allowing access to various utilities, including memory management functions.

### Main Function

```zig
pub fn main() !void {
```

The `main` function is the entry point of the program. The `!void` return type indicates that the function can return an error.

### Allocating Memory

```zig
const allocator = std.heap.page_allocator;
```

Here, the code defines `allocator` as the page allocator from the standard library, which is used for allocating memory.

```zig
var number = try allocator.alloc(i32, 1);
```

This line allocates memory for a single integer (`i32`). The `try` keyword is used to handle any potential errors that may occur during allocation. If the allocation fails, the function will return an error.

### Setting and Printing the Value

```zig
number[0] = 42;
```

After successfully allocating memory, this line sets the value of the allocated integer to **42**. The allocated memory is treated as an array, so `number[0]` accesses the first (and only) element.

```zig
std.debug.print("The value is: {}\n", .{number[0]});
```

This line prints the value of the integer to the console. The `{}` is a placeholder for the value, which is provided in the second argument as a tuple.

### Deallocating Memory

```zig
allocator.free(number);
```

Finally, this line deallocates the memory that was previously allocated for the integer.

:::{.callout-important}
With great power comes great responsibility!

It's important to free allocated memory to prevent memory leaks.  Explicit allocation and deallocation makes it easy to see exactly what the code's doing, but this places the burden of responsible memory management on you!
:::

# Object Orientation

In Zig, classes as found in object-oriented languages like C++ or Java do not exist. Instead, Zig uses a combination of structs and functions to achieve similar functionality.  (This is very similar to Rust.)

You can define a struct to hold data and then create functions that operate on that data, effectively mimicking class behavior:

```{.zig filename="struct.zig"}
const std = @import("std");

pub const MyClass = struct {
    value: i32,

    pub fn new(value: i32) MyClass {
        return MyClass{ .value = value };
    }

    pub fn increment(self: *MyClass) void {
        self.value += 1;
    }

    pub fn getValue(self: *MyClass) i32 {
        return self.value;
    }
};

pub fn main() void {
    var myObject = MyClass.new(10);
    myObject.increment();
    const currentValue = myObject.getValue();
    std.debug.print("Current Value: {}\n", .{currentValue});
}
```

Output:

```txt
Current Value: 11
```

## Code Breakdown

### Defining MyClass

```zig
pub const MyClass = struct {
    value: i32,
```

Here, `MyClass` is defined as a public structure (`struct`) that contains a single field, `value`, which is of type `i32` (32-bit integer).

### Constructor Method

```zig
pub fn new(value: i32) MyClass {
    return MyClass{ .value = value };
}
```
* This is a public function `new` that acts as a constructor for `MyClass`.
* It takes an `i32` parameter `value` and returns a new instance of `MyClass` initialized with that value.

### Increment Method

```zig
pub fn increment(self: *MyClass) void {
    self.value += 1;
}
```

* The `increment` function is a public method that takes a mutable pointer to `MyClass` (`self: *MyClass`).
* It increments the `value` field of the instance by 1.

### Getter Method

```
pub fn getValue(self: *MyClass) i32 {
    return self.value;
}
```

* The `getValue` function is a public method that returns the current value of the `value` field.
* It also takes a mutable pointer to `MyClass`.

### Main Function

```zig
pub fn main() void {
    var myObject = MyClass.new(10);
    myObject.increment();
    const currentValue = myObject.getValue();
    std.debug.print("Current Value: {}\n", .{currentValue});
}
```

* The `main` function is the entry point of the program.
* It creates an instance of `MyClass` called `myObject`, initialized with the value `10`.
* It calls the `increment` method on `myObject`, which increases its `value` from `10` to `11`.
* Finally, it retrieves the current value using `getValue` and prints it to the console using `std.debug.print`.

# Cross-Compilation

Returning to our "Hello World" program:

```{.zig filename="hello.zig"}
const std = @import("std");

pub fn main() !void {
    const stdout = std.io.getStdOut().writer();

    try stdout.print("Hello, World!\n", .{});
}
```

Since I'm running in Linux, building without specifying a target produces a Linux binary:

```bash
zig build-exe hello.zig

file hello
```

```txt
hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, with debug_info, not stripped
```

But, we can easily target other architectures:

```bash
zig build-exe hello.zig -target x86_64-windows

file hello.exe
```

```txt
hello.exe: PE32+ executable (console) x86-64, for MS Windows, 7 sections
```

# Weaknesses and Limitations

Zig is a fairly new language. I expect that many of these issues will be resolved or improved over time. But, for the time being, there are quite a few issues to consider.

## Limited Ecosystem and Libraries

* **Fewer Libraries**: Compared to more established languages like Python or Java, Zig has a smaller ecosystem of libraries and frameworks. This can make it challenging to find pre-built solutions for common problems.
* **Community Size**: The community is still growing, which means fewer resources, tutorials, and community support compared to more mature languages.

## Learning Curve

* **Syntax and Concepts**: While Zig aims for simplicity, its syntax and concepts may be unfamiliar to developers coming from other languages, particularly those used to garbage-collected languages.
* **Manual Memory Management**: Zig requires developers to manage memory manually, which can lead to increased complexity and potential for errors, especially for those not accustomed to this approach.

## Tooling and IDE Support

* **Limited IDE Support**: The tooling and IDE support for Zig are not as robust as those for more established languages. This can affect productivity, especially for developers who rely heavily on integrated development environments.  (The Zig extension in VS Code does provide a decent experience, though.)
* **Build System**: Zig's build system is still evolving, and while it offers unique features, it may not be as mature or user-friendly as those found in other languages.

# Summary

I'm not sure it's ready for "prime time", but it's definitely an interesting language and ecosystem.  I'll be thinking about some small starter projects I can use to get more familiar with it and test its limitations.
