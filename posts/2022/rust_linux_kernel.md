---
title: "Rust In the Linux Kernel"
author: "Jim Carr"
date: "2022-09-10"
categories: [Rust]
---

**Update**: It looks like Rust [won’t make it for 6.0](https://www.phoronix.com/review/linux-60-features). Hopefully we’ll still see it fairly soon!

**2nd Update**: Initial Rust support is now targeted for 6.1. The first merge into the mainline code was [completed on October 3rd](https://www.phoronix.com/news/Rust-Is-Merged-Linux-6.1).

----

[Back in July 2020](https://www.phoronix.com/news/Linux-Kernel-Rust-Path-LPC2020), upstream Linux developers started investigating a path for adding Rust code to the Linux kernel. That discussion was expanded upon during the Linux Plumbers Conference a month later.

Miguel Ojeda, the kernel developer primarily responsible for this effort, has [released multiple patch series](https://www.phoronix.com/news/Rust-v8-For-Linux-Kernel) for this support, with v8 of the initial 43.6k lines of code being added to the development branch in August 2022.

It’s looking like it [might show up officially in Linux Kernel 6.0](https://news.itsfoss.com/linux-kernel-6-0-reveal/), which as of September 4th is at release candidate 4.
