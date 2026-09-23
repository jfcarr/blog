---
title: "Rust Foundation"
author: "Jim Carr"
date: "2021-02-08"
categories: [Rust]
---
The [Rust programming language](https://www.rust-lang.org/) has a new home! A collaboration between Amazon Web Services, Huawei, Google, Microsoft and Mozilla has resulted in creation of the [Rust Foundation](https://foundation.rust-lang.org/), a support and governance organization that will provide stewardship of the increasingly popular language.

The language began as a side project inside Mozilla, intended as an alternative to C/C++, but with memory safety, while still being performant. To achieve this, it implements a borrow checker for reference validation. The language was refined during creation of Servo, an experimental browser engine.

Hello World, in Rust:

```rust
fn main() {
   println!("Hello World!");
}
```

As it has matured, the Rust language has gained visibility, and uptake by large, respected organizations has helped:

* Google is funding a Rust-based project to make the Apache web server safer.
* Microsoft recently formed a Rust team, and is using the language to rewrite some core Windows APIs.
* Amazon Web Services recently launched Bottlerocket, a new Linux distribution for containers. It includes a build system written mostly in Rust.

In August 2020, Mozilla laid off 250 of its 1,000 employees worldwide as part of a corporate restructuring. These layoffs included most of the Rust team, and the Servo team was completely disbanded. The future of Rust seemed to be in serious danger.

But, within a week, the Rust Core Team announced that plans for a Rust foundation were underway. Initially, the foundation would be taking ownership of all trademarks and domain names, and also take financial responsibility for their costs.

Looks like Rust has a bright future!
