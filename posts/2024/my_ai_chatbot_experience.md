---
title: "My AI Chatbot Experience"
author: "Jim Carr"
date: "2024-03-03"
categories: [AI]
draft: true
---

You can’t escape AI these days. Various industries and domains have incorporated AI to varying degrees of effectiveness. I think that the strangest AI experience I’ve had so far is the AI chatbot at my local Rally’s restaurant drive-thru.

As a software engineer, though, its potential as a coding assistant has always seemed promising. A few months ago, I tried out Google Bard, asking it some questions specific to the project I was working on. Some of the answers were impressive, but others were not. Ultimately, I felt that it wasn’t good enough for daily use. Recently, though, with the rebranding of Bard to Gemini, and the release of Gemini Advanced, I decided to try it again. Boy, what a difference a few months makes! In summary, I find it indispensable at this point, and usually use it several times a day.

Here are a few examples:

## Troubleshooting Active Directory VM Connectivity

### My Question

I'm trying to connect to an Active Directory VM in Azure from an Azure App Service, using a PrincipalContext in C#, and I'm getting “The server could not be contacted”

### Gemini’s Answer

Absolutely, let's troubleshoot why you're getting “The server could not be contacted” when connecting to your Active Directory (AD) VM from your Azure App Service. Here's a breakdown of potential causes and debugging steps:

1. Network Connectivity

* Virtual Network Integration: Ensure that your Azure App Service and Active Directory VM reside within the same Azure Virtual Network (VNet) or have peered VNets. If they are in separate VNets, confirm there's connectivity between them.
* Network Security Groups (NSGs): Verify that both the App Service and AD VM have NSG rules allowing inbound traffic on the necessary ports for Active Directory communication (e.g., TCP 389 for LDAP, TCP 445 for SMB, etc.).
* DNS Resolution: Check that your App Service can correctly resolve the hostname or IP address of your AD VM. Configure DNS settings appropriately on your VNet.

2. Active Directory Configuration

* Domain Controller Reachability: Make sure that the Active Directory Domain Controller (DC) within the VM is running and reachable over the network.
* Firewall: If there's a firewall on the AD VM, ensure it permits the necessary inbound connections for AD protocols.

3. C# Code and Authentication

* PrincipalContext Constructor: Double-check that you're using the correct constructor for the `PrincipalContext` object:

```csharp
using (var context = new PrincipalContext(ContextType.Domain, "yourdomain.com"))
```

Replace “yourdomain.com” with your actual domain name.

* Credentials: If you need to authenticate with specific credentials, provide them in the `PrincipalContext` constructor:

```csharp
using (var context = new PrincipalContext(ContextType.Domain, "yourdomain.com", "username", "password"))
```

* Service Principal: For app-level authentication, consider using a Service Principal with appropriate permissions on the AD.

Debugging Steps

1. Simplified Connection Test: Use a basic network tool like `ping` or `telnet` from your App Service to see if you can reach the AD VM's IP address and the relevant Active Directory ports.
2. Detailed Logging: Enable more detailed logging within your C# application to potentially get more specific error messages about the connection failure.
3. Azure Diagnostics: Use Azure App Service Diagnostics and Azure Network Watcher to capture logs and network traces.

Additional Considerations

* Hybrid Scenarios: If you have a hybrid environment (Azure AD and on-premises AD), ensure that Azure AD Connect is syncing correctly and the App Service has proper access to your on-premises AD environment.
* Managed Service Identity (MSI): If applicable, consider using MSI to streamline authentication, making code cleaner and more secure.

Example Code (with placeholders):

```csharp
using System.DirectoryServices.AccountManagement;
 
try
{
	using (var context = new PrincipalContext(ContextType.Domain, "yourdomain.com"))
	{
    	bool isValid = context.ValidateCredentials("username", "password"); // Replace with real credentials
    	// Further operations if credentials are valid
	}
}
catch (Exception ex)
{
	// Log the detailed exception here
	Console.WriteLine("Error connecting to AD: " + ex.Message);
}

```

Let me know if you can provide the following, and I'll tailor the guidance further:

Relevant portions of your C# code Your Azure network setup (VNets, subnets, NSGs) Whether this is a hybrid AD scenario

Let's work together to resolve this!

### Accuracy

As you can see, Gemini provided a comprehensive answer, with multiple suggestions. This topic is definitely not within my area of expertise, but I passed some information along to others on my team, and the suggestions around Virtual Network Integration and Network Security Groups (NSGs) were useful.

## Excluding Certain Items On List Filter

### My Question

This is C#

I have a list of text strings:

```csharp
List<string> exclusionText = new() { "(Privileged User)", "(Service Account)", "(Contractor)", "(Duplicate)" };
```

I'm iterating through another list called logMessages. For each string in logMessages, I want to take an action only if the string does NOT contain any of the strings in exclusionText.

I have this working using LINQ, but my code seems overly verbose. Please suggest a concise way to accomplish this.

### Gemini’s Answer

Here's how you can do this in C#, along with explanations of the different approaches:

1. Using a Loop and `Contains`:

```csharp
List<string> exclusionText = new() { "(Privileged User)", "(Service Account)", "(Contractor)", "(Duplicate)" };
List<string> logMessages = new() { /* your log messages here */ };
 
foreach (string message in logMessages)
{
	bool shouldTakeAction = true;  // Assume action is needed initially
 
	// Check if the message contains any exclusion text
	foreach (string exclusion in exclusionText)
	{
    	if (message.Contains(exclusion))
    	{
        	shouldTakeAction = false;
        	break; // No need to check further exclusions
    	}
	}
 
	if (shouldTakeAction)
	{
    	// Take action on the message here
    	Console.WriteLine(message);
	}
}
```

Explanation:

* We iterate through each `message` in `logMessages`.
* An inner loop checks if the `message` contains any of the strings in `exclusionText`.
* If a match is found, `shouldTakeAction` is set to `false` and we break.
* If the `message` doesn't contain any excluded text, the action is taken.

2. Using LINQ with `Any`:

```csharp
List<string> exclusionText = new() { "(Privileged User)", "(Service Account)", "(Contractor)", "(Duplicate)" };
List<string> logMessages = new() { /* your log messages here */ };
 
var filteredMessages = logMessages.Where(message => !exclusionText.Any(text => message.Contains(text)));
 
foreach (string message in filteredMessages)
{
	// Take action on the message here
	Console.WriteLine(message);
}
```

Explanation:

* LINQ: LINQ (Language-Integrated Query) provides a concise syntax for working with collections.
* Where: Filters the `logMessages` based on a condition.
* Any: Checks if any element in `exclusionText` is found within the message. If `Any` returns `true`, it means the message should be excluded. We use the `!` to negate the result.

Which One Is Better?

* The LINQ approach is generally more concise and readable, especially for those familiar with LINQ.
* If you prioritize clarity over raw performance, the LINQ method is often preferred.
* For very large lists or performance-critical scenarios, the first approach (with loops) might have a slight performance edge.

Let me know if you'd like to explore ways to potentially optimize this code further!

### Accuracy

I thought Gemini did an excellent job here. The first suggestion describes a way to accomplish this without using LINQ, and the second is a clean usage of Any(). The Any() answer was key to me refactoring my code.

## Determine if a List Only Contains Entries With a Certain MessageType

### My Question

Simplify this C# code:

```csharp
var entryTypesLogged = entriesToMail.Select(x => x.MessageType).Distinct().ToList();
 
var isInfoOnly = entryTypesLogged.Count == 1 && entryTypesLogged[0] == "[INFO]";
```

### Gemini’s Answer

Absolutely! Here's how you can simplify that C# code and a breakdown of the potential optimizations:

Optimized Version

```csharp
var isInfoOnly = entriesToMail.All(x => x.MessageType == "[INFO]");
```

Explanation

* LINQ's `All()`: This concisely checks if all elements in the `entriesToMail` collection have a `MessageType` of “[INFO]”. This directly determines if the log contains only info entries.
* Efficiency: The `All()` method has the potential to stop iterating as soon as it finds an entry that isn't “[INFO]”, improving efficiency in scenarios where you have non-info entries within the data.

Why This is Better

* Clarity: The `All()` method expresses the intent of the code very clearly. You're directly checking if all entries are of the type “[INFO]”.
* Potential Performance: This can offer a performance gain over the original code, especially if you have a larger dataset where the first few elements might not be “[INFO]”.

Let me know if you'd like to explore other ways to potentially refactor this code, or if you have another snippet to optimize!

### Accuracy

Another good answer: Nice, clean use of Any(), again.

## Dictionary Initialization

A more “general” question.

### My Question

Dictionary initialization example for C#

### Gemini’s Answer

Absolutely! Here's a breakdown of how to initialize dictionaries in C# with various examples:

1. Collection Initializer Syntax

This is the most common and concise way to initialize a dictionary:

```csharp
var myDictionary = new Dictionary<string, int>()
{
	{ "apple", 3 },
	{ "orange", 1 },
	{ "banana", 5 }
};
```

* Use `Dictionary<string, int>` to specify that the keys are strings, and values are integers.
* `var` allows the compiler to infer the type.

2. Traditional Initialization with the `Add` Method

```csharp
var myDictionary = new Dictionary<string, int>();
myDictionary.Add("apple", 3);
myDictionary.Add("orange", 1);
myDictionary.Add("banana", 5);
```

3. Index Initializer Syntax

This method is similar to Collection Initializer, providing a cleaner look:

```csharp
var myDictionary = new Dictionary<string, int>()
{
	["apple"] = 3,
	["orange"] = 1,
	["banana"] = 5
};
```

Initializing with Objects

You can use object initializers for more complex key or value types:

```csharp
public class Student
{
   public string Name { get; set; }
   public int ID { get; set; }
}
 
var studentRecords = new Dictionary<int, Student>()
{
	{ 111, new Student { Name = "Alice", ID = 101 }},
	{ 222, new Student { Name = "Bob", ID = 102 }}
};
```

Key Points

* Dictionaries store key-value pairs.
* Keys must be unique within a dictionary.
* Choose the initialization method that best suits your readability preferences and how your data is structured.

Let me know if you'd like more specific examples or scenarios!

### Accuracy

Way more detail than I needed, but it wasn’t wrong!

## Environment Variable for Azure App Service

### My Question

Add environment variable for Azure App Service

### Gemini’s Answer

Absolutely, here's how to add environment variables to your Azure App Service, along with explanations about why they're useful:

Why Use Environment Variables in Azure App Service?

* Configuration: They allow you to separate sensitive configuration details (database connection strings, API keys, etc.) from your code, improving security and portability.
* Environment-Specific Settings: You can tailor application behavior to the environment it's running in (development, staging, production) by setting different values for the same variable
* Avoiding Hardcoding: Environment variables provide flexibility because you don't have to modify code to change configuration.

Methods to Add Environment Variables

1. Azure Portal

* Navigate to your Azure App Service instance in the Azure Portal.
* Go to Configuration in the left menu.
* Select Application settings.
* Click + New application setting
* Enter your environment variable Name and Value.
* Click OK, then Save at the top.

2. Azure CLI

```bash
az webapp config appsettings set --resource-group <resource_group_name> --name <app_name> --settings <key>=<value> <key2>=<value2> ...
```

* Replace placeholders like `<resource_group_name>` and `<app_name>` with your specific details.

3. Deployment Tools (ARM templates, etc.)

* If you're using deployment tools like ARM templates, you can include environment variables as parameters in the template. This automates the process.

Accessing Environment Variables in Your App

The way you access environment variables depends on your programming language and framework:

* .NET Core: `Environment.GetEnvironmentVariable(“VARIABLE_NAME”)`
* Node.js: `process.env.VARIABLE_NAME`
* Python: `os.environ['VARIABLE_NAME']`
* And so on…

Important Notes

* App Restart: Changes to environment variables in the Azure Portal often require an app restart to take effect.
* Security: Exercise caution storing highly sensitive data in environment variables. Consider using Azure Key Vault for such information.

Let me know if you would like me to elaborate on a specific method or programming language for accessing your newly-set environment variables!

### Accuracy

Gemini provided three approaches. The first answer (using the Azure Portal) was what I was looking for.

## Smart Pointers in C++

### My Question

Using smart pointers in C++

### Gemini’s Answer

Absolutely! Here's a comprehensive guide on using smart pointers in C++, covering their benefits, common types, and best practices:

Understanding Smart Pointers

* What are they? Smart pointers are wrapper classes designed to manage dynamically allocated memory (memory on the heap). They automate the process of deleting heap-allocated objects when they are no longer needed.
* Why use them?
  * Prevent memory leaks: A major issue with raw pointers is forgetting to call `delete`. Smart pointers take care of this automatically.
  * Resource Acquisition Is Initialization (RAII): Smart pointers tie object lifetime to their own scope. This ensures proper resource management.
  * Exception safety: Even if an exception occurs, smart pointers ensure that the memory is deallocated properly.

Common Types of Smart Pointers

1. `unique_ptr`

* Exclusive ownership. Only one `unique_ptr` can point to an object at a time.
* Ideal when you need sole ownership of a resource.
* Example:

```cpp
#include <memory>
 
class MyClass { /* ... */ };
 
int main() {
  std::unique_ptr<MyClass> ptr(new MyClass());
  // Use ptr->... to access object members
 
  // No need to call delete, happens automatically when ptr goes out of scope
}
```

2. `shared_ptr`

* Shared ownership. Multiple `shared_ptr` instances can point to the same object.
* Object is deleted when the last `shared_ptr` referencing it is destroyed or reassigned.
* Use when multiple parts of your code need to manage the same resource.
* Example:

```cpp
#include <memory>
 
class Asset { /* ... */ };
 
void process(std::shared_ptr<Asset> resource) {
  // ...
}
 
int main() {
  std::shared_ptr<Asset> assetPtr(new Asset());  
  process(assetPtr);
  // ... more code using assetPtr
}
```

3. `weak_ptr`

* Non-owning reference. Used to observe a resource managed by a `shared_ptr` without affecting its lifetime.
* Prevents circular references that could lead to memory leaks.
* Use cases include caching, temporary access, and breaking dependency cycles.
* Example:

```cpp
std::weak_ptr<Asset> weakAsset = assetPtr;
// Can check if the asset still exists:
if (auto sharedAsset = weakAsset.lock()) {
  // Use sharedAsset
}
```

Best Practices

* Prefer smart pointers over raw pointers: In almost all modern C++ scenarios, you should default to using smart pointers.
* Choose the right type: Consider ownership needs (unique vs. shared).
* Avoid circular references with `weak_ptr`: Be careful with `shared_ptr` in complex object relationships. `weak_ptr` can help.
* Be aware of potential overhead: Smart pointers have a small overhead compared to raw pointers, but this is generally negligible in modern systems.

Additional Considerations

* Custom deleters: Smart pointers can work with custom deleters for more complex deallocation scenarios.
* C++11 onwards: Smart pointers are a core part of modern C++ (C++11 and later).

Let me know if you'd like more in-depth examples, scenarios where specific smart pointers shine, or how to use them in advanced C++ memory management patterns!

### Accuracy

I hadn’t worked in C++ for a long time. I thought this was a “detailed, but accessible” answer. It really helped me understand the topic.

## C# To Rust

### My Question

Convert this C# to Rust:

```csharp
class Product
{
   public int Id { get; set; }
   public string Name { get; set; }
   public decimal Price { get; set; }
}
 
List<Product> products = new List<Product> {
   new Product { Id = 1, Name = "Laptop", Price = 800 },
   new Product { Id = 2, Name = "Tablet", Price = 350 },
   new Product { Id = 3, Name = "Monitor", Price = 200 }
};
 
// Find products with price greater than 500
var expensiveProducts = products.Where(p => p.Price > 500);
 
// Find the first product whose name starts with "T"
var productStartingWithT = products.FirstOrDefault(p => p.Name.StartsWith("T"));
 
// Check if any product has Id 5
var hasProductWithId5 = products.Any(p => p.Id == 5);
```

### Gemini’s Answer

Absolutely! Let's break down how to convert your C# code into its Rust equivalent.

Rust Translation

Here's a Rust version that mirrors the functionality of your C# code:

```rust
use std::option::Option; // For handling Option in FirstOrDefault-like behavior
 
// Define the Product struct
struct Product {
	id: i32,     	// Rust typically uses i32 for integers
	name: String,    
	price: f64,  	// Consider f64 for floating-point accuracy
}
 
fn main() {
	// Create a vector of products
	let products = vec![
    	Product { id: 1, name: "Laptop".to_string(), price: 800.0 },
    	Product { id: 2, name: "Tablet".to_string(), price: 350.0 },
    	Product { id: 3, name: "Monitor".to_string(), price: 200.0 },
	];
 
	// Find products with price greater than 500
	let expensive_products = products
    	.iter()
    	.filter(|p| p.price > 500.0)
    	.collect::<Vec<_>>();
 
	// Find the first product whose name starts with "T"
	let product_starting_with_t = products
    	.iter()
    	.find(|p| p.name.starts_with("T"));
 
	// Check if any product has Id 5
	let has_product_with_id_5 = products
    	.iter()
    	.any(|p| p.id == 5);
 
	// Print results
	println!("Expensive products: {:?}", expensive_products);
	println!("Product starting with T: {:?}", product_starting_with_t);
	println!("Has product with Id 5: {}", has_product_with_id_5);
}
```

Key Considerations and Explanations

* Structs: Rust uses `struct` to define data structures like C#'s `class`.
* Data Types: I've used `i32` for the `id` (similar to C#'s `int`) and `f64` for the `price` to accommodate potential decimal values. Rust's `String` is the counterpart to C#'s `string`.
* Ownership: Rust has strict ownership rules. We use `.to_string()` to create owned `String` instances, as `.iter()` in the following lines borrows the data.
* Iterators and Functional Style: Rust heavily favors iterators and methods like `filter`, `find`, and `any` for data manipulation. These provide a concise, functional programming approach.
* Option Handling: Rust doesn't have a direct equivalent to `FirstOrDefault`. The `find` method returns an `Option<T>`, which can be either `Some(value)` or `None`.

Caveats

* Decimal Type: If you need precise handling of decimal values, consider using a dedicated decimal type library in Rust.
* Error Handling: A more robust Rust translation would incorporate better error handling instead of simply unwrapping the `Option`.

Let me know if you'd like more explanation about specific parts of the Rust code or want to explore alternative ways to approach the translation!

### Accuracy

I’m much more comfortable with C# than Rust. Seeing the code examples side-by-side helped me understand using Rust iterators and how their functionality compares to LINQ in C#. I thought the “caveats” were a nice touch, too.

## Caveats

Even though the quality of answers has improved dramatically, serious issues do remain. Including, but not limited to:

* **Ethical Concerns** In order to build its [LLM](https://en.wikipedia.org/wiki/Large_language_model), an AI engine scrapes massive amounts of information from the web. There are two problems with this:
  * The quality of data sources varies a great deal. And, even when the quality is good, you're at the mercy of the AI engine's ability to contextualize it properly. It is, after all, only a large statistical model.
  * The AI engine doesn't provide credit to the original source. (This is particularly problematic in code generation, as you may be using code provided by the AI without respecting the original license of the source it was scraped from.)
* **Overcompensation** In an effort to “do the right thing”, results can [seriously misfire](https://www.theverge.com/2024/2/22/24079876/google-gemini-ai-photos-people-pause).
* Curation The answers you get are filtered through the lens of the AI. Unlike a web search, where you receive results from many sources and can pick-and-choose which of those you want to follow through on, asking your questions via the AI means the AI preselects the answers for you. For example:
  * I asked Gemini about geolocation in a Blazor WebAssembly application. It stepped me through a JavaScript interop solution, which was detailed and helpful, but it failed to mention any of the pre-built packages available in Nuget. Using a Nuget package will probably save me time.

I hope that these will be addressed over time.

## Summary

Gemini provides a lot more hits than misses now. Answers are sometimes incomplete, or require a bit of tweaking, but this comes down to how well Gemini is able to contextualize a given query. It generally does a good job.

I feel like it’s most useful when you’re using it to assist you with a language or topic that you already have some familiarity with. This helps you understand why Gemini provided the answer it did, and you’re better equipped to tweak the answer as needed.

My take? It’s time for developers to add AI assistance to their toolbox.
