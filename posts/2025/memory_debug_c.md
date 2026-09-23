---
title: "Memory Debugging in C"
description: "_With a massive existing codebase, along with a talent pool with many decades of experience, what steps can we take to improve the quality and safety of code in older languages, C in particular?_"
author: "Jim Carr"
date: "2025-09-13"
categories: [C]
draft: false
---

<table>
   <tr>
      <td>The landscape of memory safety in low-level programming languages, particularly C and C++, is increasingly under scrutiny due to the significant security vulnerabilities associated with unsafe memory practices. Recent reports and initiatives highlight the urgent need for a shift towards memory-safe languages to mitigate these risks.</td>
      <td><img src="images/sherlock-holmes.png"></td>
   </tr>
</table>

# Key Findings on Memory Safety Vulnerabilities

1. **Prevalence of Memory Safety Issues**: 
   - A staggering **70%** of vulnerabilities in products from major tech companies like Microsoft and Google are attributed to memory safety issues. This includes common problems such as buffer overflows and use-after-free bugs.
   - In mobile operating systems, studies indicate that **60-90%** of vulnerabilities are related to memory safety.

2. **Types of Memory Safety Errors**:
   - **Out-of-Bounds Errors**: Occur when a program accesses memory outside the allocated range, potentially leading to data corruption or unauthorized access.
   - **Use-After-Free Bugs**: Happen when a program continues to use memory after it has been freed, which can lead to unpredictable behavior and security breaches.

3. **Historical Context**: 
   - Notable security incidents, such as the **Heartbleed** vulnerability, which compromised sensitive data across numerous websites, were rooted in memory safety flaws. Such incidents underscore the critical need for improved memory management practices.

## Recommendations and Initiatives

1. **Adoption of Memory-Safe Languages**:
   - The **U.S. White House Office of the National Cyber Director** has recommended transitioning to memory-safe languages like **Rust**, **Go**, **C#**, **Java**, and **Swift**. These languages incorporate built-in protections that help prevent common memory-related vulnerabilities.
   - The **National Security Agency (NSA)** and **Cybersecurity and Infrastructure Security Agency (CISA)** have jointly emphasized the importance of adopting memory-safe languages as part of a comprehensive cybersecurity strategy.

2. **The Memory Safety Continuum**:
   - Released by the **Open Source Security Foundation (OpenSSF)**, this framework provides a structured approach for organizations to assess and improve their memory safety practices. It encourages incremental improvements rather than viewing memory safety as a binary state.

3. **Challenges in Transition**:
   - While the shift to memory-safe languages is encouraged, organizations face challenges such as performance considerations, the complexity of legacy codebases, and the need for developer training. Immediate adoption may not be feasible, necessitating a phased approach.

> [_But_, with a massive existing codebase, along with a talent pool with many decades of experience, what steps can we take to improve the quality and safety of code in older languages, C in particular?]{.mark}
>
> [Let's talk about that!]{.mark}

# Tools

* **GCC**, the GNU C compiler.  It's preinstalled on most Linux distributions.
* **Clang Tidy**, provides linting and code modernization.
* **Address Sanitizer**, a compile flag built into the gcc compiler.
* **Valgrind**, a tool used for memory debugging, memory leak detection, and profiling.

## Install Tools

```bash
sudo apt install clang-tidy valgrind
```

Verify that Clang Tidy and Valgrind are installed and available:

```bash
clang-tidy --version

valgrind --version
```

Output (your versions may differ):

```txt
Ubuntu LLVM version 18.1.3
  Optimized build.

valgrind-3.22.0
```

# Memory Debugging

Let's walk through some scenarios.

## Buffer Overflow

```{.c filename="buffer_overflow.c"}
#include <stdio.h>
#include <string.h>

void vulnerableFunction() {
    char buffer[10]; // A buffer that can hold 10 characters
    printf("Enter some text: ");
    gets(buffer); // Unsafe function that does not check buffer size
    printf("You entered: %s\n", buffer);
}

int main() {
    vulnerableFunction();
    return 0;
}
```

Compile it:

```bash
gcc -g -o buffer_overflow buffer_overflow.c
```

We immediately get a warning from the compiler, as `gets()` is inherently unsafe and has been deprecated in the C11 standard:

```txt
buffer_overflow.c: In function ‘vulnerableFunction’:
buffer_overflow.c:8:5: warning: implicit declaration of function ‘gets’; did you mean ‘fgets’? [-Wimplicit-function-declaration]
    8 |     gets(buffer); // Unsafe function that does not check buffer size
      |     ^~~~
      |     fgets
/usr/bin/ld: /tmp/ccbCyqjb.o: in function `vulnerableFunction':
buffer_overflow.c:(.text+0x3c): warning: the `gets' function is dangerous and should not be used.
```

Let's "fix" it, but make a mistake on purpose:

```{.c filename="buffer_overflow.c"}
#include <stdio.h>
#include <string.h>

void vulnerableFunction()
{
    char buffer[10];

    printf("Enter some text: ");
    fgets(buffer, 20, stdin);

    printf("You entered: %s\n", buffer);
}

int main()
{
    vulnerableFunction();

    return 0;
}
```

We've switched to the safe fgets function, but "accidently" specified a size that's too large.  When we compile:

```bash
gcc -g -o buffer_overflow_updated buffer_overflow_updated.c
```

```txt
buffer_overflow_updated.c: In function ‘vulnerableFunction’:
buffer_overflow_updated.c:9:5: warning: ‘fgets’ writing 20 bytes into a region of size 10 overflows the destination [-Wstringop-overflow=]
    9 |     fgets(buffer, 20, stdin);
      |     ^~~~~~~~~~~~~~~~~~~~~~~~
buffer_overflow_updated.c:6:10: note: destination object ‘buffer’ of size 10
    6 |     char buffer[10];
      |          ^~~~~~
In file included from buffer_overflow_updated.c:1:
/usr/include/stdio.h:654:14: note: in a call to function ‘fgets’ declared with attribute ‘access (write_only, 1, 2)’
  654 | extern char *fgets (char *__restrict __s, int __n, FILE *__restrict __stream)
      |              ^~~~~
```

In this scenario, the compiler does a good job of catching the mistake.  Let's update it and do it the right way:

```{.c filename="buffer_overflow.c"}
#include <stdio.h>
#include <string.h>

void vulnerableFunction()
{
    char buffer[10];

    printf("Enter some text: ");
    fgets(buffer, sizeof(buffer), stdin);

    printf("You entered: %s\n", buffer);
}

int main()
{
    vulnerableFunction();

    return 0;
}
```

The code now compiles with no errors.  For this scenario, we didn't need to use any additional tools to find the problem.  But, the compiler can't catch everything.  Consider this example:

```{.c filename="buffer_overflow_dynamic.c"}
#include <stdio.h>
#include <string.h>

void vulnerableFunction(int size)
{
    char buffer[10];  // A buffer that can hold 10 characters
    char input[size]; // Variable-length input buffer

    printf("Enter some text: ");

    // Unsafe use of fgets: size is determined at runtime:
    fgets(input, size, stdin); // Read input into the variable-length buffer

    // Potential buffer overflow if input exceeds buffer size
    // No bounds checking on the copy
    strcpy(buffer, input); // Unsafe copy that can overflow buffer

    printf("You entered: %s\n", buffer);
}

int main()
{
    int size = 20;

    // Call the vulnerable function with the size value:
    vulnerableFunction(size);
    return 0;
}
```

In the code above, the size of the input buffer is set to 20 (too large), but the compiler evaluates `vulnerableFunction`, where the input buffer size is treated as dynamic, and so the error isn't caught:  This code compiles with no errors or warnings, but running it gives us something like this:

```txt
Enter some text: Some text that is too large on purpose!
You entered: Some text that is t
*** stack smashing detected ***: terminated
Aborted (core dumped)
```

If we run the code through `clang-tidy`, we get some hints about unsafe behavior:

```bash
clang-tidy buffer_overflow_dynamic.c
```

```txt
1 warning generated.
buffer_overflow_dynamic.c:16:5: warning: Call to function 'strcpy' is insecure as it does not provide bounding of the memory buffer. Replace unbounded copy functions with analogous functions that support length arguments such as 'strlcpy'. CWE-119 [clang-analyzer-security.insecureAPI.strcpy]
   16 |     strcpy(buffer, input); // Unsafe copy that can overflow buffer
      |     ^~~~~~
```

If we re-compile with the address sanitizer flag:

```bash
gcc -g -fsanitize=address -g buffer_overflow_dynamic.c -o buffer_overflow_dynamic
```

...and then run, the failure does get reported, again with information about the point of failure:

```txt
Enter some text: Some text that is too large on purpose!
=================================================================
==1999384==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x728e9730002a at pc 0x728e998a7923 bp 0x7fff2dc230e0 sp 0x7fff2dc22888
WRITE of size 20 at 0x728e9730002a thread T0
    #0 0x728e998a7922 in strcpy ../../../../src/libsanitizer/asan/asan_interceptors.cpp:563
    #1 0x64151ad9347d in vulnerableFunction buffer_overflow_dynamic.c:16
    #2 0x64151ad9353a in main buffer_overflow_dynamic.c:26
    #3 0x728e9942a1c9 in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #4 0x728e9942a28a in __libc_start_main_impl ../csu/libc-start.c:360
    #5 0x64151ad931e4 in _start (buffer_overflow_dynamic+0x11e4) (BuildId: 953df7f0607850ac9a18932ea140e86278e57fa6)

Address 0x728e9730002a is located in stack of thread T0 at offset 42 in frame
    #0 0x64151ad932b8 in vulnerableFunction buffer_overflow_dynamic.c:5

  This frame has 1 object(s):
    [32, 42) 'buffer' (line 6) <== Memory access at offset 42 overflows this variable
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork
      (longjmp and C++ exceptions *are* supported)
SUMMARY: AddressSanitizer: stack-buffer-overflow ../../../../src/libsanitizer/asan/asan_interceptors.cpp:563 in strcpy
Shadow bytes around the buggy address:
  0x728e972ffd80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e972ffe00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e972ffe80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e972fff00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e972fff80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x728e97300000: f1 f1 f1 f1 00[02]f3 f3 00 00 00 00 00 00 00 00
  0x728e97300080: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e97300100: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e97300180: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e97300200: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x728e97300280: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
==1999384==ABORTING
```

So, in this case (similar to clang-tidy) we do at least get the failure point, which gives us something to work backwards from.

Finally, if we run through Valgrind:

```bash
valgrind --leak-check=full --show-leak-kinds=all ./buffer_overflow_dynamic
```

```txt
==2008869== Memcheck, a memory error detector
==2008869== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==2008869== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==2008869== Command: ./buffer_overflow_dynamic
==2008869== 
Enter some text: Some text that is too long on purpose!
You entered: Some text that is t
*** stack smashing detected ***: terminated
==2008869== 
==2008869== Process terminating with default action of signal 6 (SIGABRT)
==2008869==    at 0x490EB2C: __pthread_kill_implementation (pthread_kill.c:44)
==2008869==    by 0x490EB2C: __pthread_kill_internal (pthread_kill.c:78)
==2008869==    by 0x490EB2C: pthread_kill@@GLIBC_2.34 (pthread_kill.c:89)
==2008869==    by 0x48B527D: raise (raise.c:26)
==2008869==    by 0x48988FE: abort (abort.c:79)
==2008869==    by 0x48997B5: __libc_message_impl.cold (libc_fatal.c:134)
==2008869==    by 0x49A6C18: __fortify_fail (fortify_fail.c:24)
==2008869==    by 0x49A7EA3: __stack_chk_fail (stack_chk_fail.c:24)
==2008869==    by 0x1092C8: vulnerableFunction (buffer_overflow_dynamic.c:19)
==2008869==    by 0x1092EB: main (buffer_overflow_dynamic.c:26)
==2008869== 
==2008869== HEAP SUMMARY:
==2008869==     in use at exit: 2,048 bytes in 2 blocks
==2008869==   total heap usage: 2 allocs, 0 frees, 2,048 bytes allocated
==2008869== 
==2008869== 1,024 bytes in 1 blocks are still reachable in loss record 1 of 2
==2008869==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==2008869==    by 0x48F51B4: _IO_file_doallocate (filedoalloc.c:101)
==2008869==    by 0x4905523: _IO_doallocbuf (genops.c:347)
==2008869==    by 0x4902F8F: _IO_file_overflow@@GLIBC_2.2.5 (fileops.c:745)
==2008869==    by 0x4903AAE: _IO_new_file_xsputn (fileops.c:1244)
==2008869==    by 0x4903AAE: _IO_file_xsputn@@GLIBC_2.2.5 (fileops.c:1197)
==2008869==    by 0x48D0CC8: __printf_buffer_flush_to_file (printf_buffer_to_file.c:59)
==2008869==    by 0x48D0CC8: __printf_buffer_to_file_done (printf_buffer_to_file.c:120)
==2008869==    by 0x48DB742: __vfprintf_internal (vfprintf-internal.c:1545)
==2008869==    by 0x48D01B2: printf (printf.c:33)
==2008869==    by 0x10926A: vulnerableFunction (buffer_overflow_dynamic.c:9)
==2008869==    by 0x1092EB: main (buffer_overflow_dynamic.c:26)
==2008869== 
==2008869== 1,024 bytes in 1 blocks are still reachable in loss record 2 of 2
==2008869==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==2008869==    by 0x48F51B4: _IO_file_doallocate (filedoalloc.c:101)
==2008869==    by 0x4905523: _IO_doallocbuf (genops.c:347)
==2008869==    by 0x4902883: _IO_file_underflow@@GLIBC_2.2.5 (fileops.c:486)
==2008869==    by 0x49055D1: _IO_default_uflow (genops.c:362)
==2008869==    by 0x48F6F79: _IO_getline_info (iogetline.c:60)
==2008869==    by 0x48F5BD3: fgets (iofgets.c:53)
==2008869==    by 0x109282: vulnerableFunction (buffer_overflow_dynamic.c:12)
==2008869==    by 0x1092EB: main (buffer_overflow_dynamic.c:26)
==2008869== 
==2008869== LEAK SUMMARY:
==2008869==    definitely lost: 0 bytes in 0 blocks
==2008869==    indirectly lost: 0 bytes in 0 blocks
==2008869==      possibly lost: 0 bytes in 0 blocks
==2008869==    still reachable: 2,048 bytes in 2 blocks
==2008869==         suppressed: 0 bytes in 0 blocks
==2008869== 
==2008869== For lists of detected and suppressed errors, rerun with: -s
==2008869== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
Aborted (core dumped)
```

Again, we get a decent amount of detail around where things went wrong.

Overall, a tricky problem!

## Out of Bounds Access

We'll be investigating an Out of Bounds error, but there are actually two problems with this code.  Do you see them?

```{.c filename="out_of_bounds.c"}
#include <stdlib.h>

int main()
{
    char* p = malloc(16);
    p[42] = 100;

    return 0;
}
```

First, let's see if the linter can find anything:

```bash
clang-tidy out_of_bounds.c
```

Output:

```txt
1 warning generated.
out_of_bounds.c:8:5: warning: Potential leak of memory pointed to by 'p' [clang-analyzer-unix.Malloc]
    8 |     return 0;
      |     ^
out_of_bounds.c:5:15: note: Memory is allocated
    5 |     char *p = malloc(16);
      |               ^~~~~~~~~~
out_of_bounds.c:8:5: note: Potential leak of memory pointed to by 'p'
    8 |     return 0;
      |     ^
```

Not a lot of detail, but we do get some hints about possible problems with our memory allocation.

Let's go ahead and compile with **gcc**:

```bash
gcc -g -o out_of_bounds out_of_bounds.c
```

The code compiles with no errors.  When we run, it also runs with no errors (probably!) and reports nothing.  I say probably because we are making two mistakes in this code:

1. Writing data outside the bounds of the space allocated for `p`, and
2. Not freeing the allocated space after we're done.

Let's try again, this time with the sanitize flag:

```bash
gcc -fsanitize=address -g -o out_of_bounds out_of_bounds.c
```

Now when we run, we actually get some useful feedback:

```txt
==2642974==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x50200000003a at pc 0x55a1ade4e1ff bp 0x7ffcaaeb3380 sp 0x7ffcaaeb3370
WRITE of size 1 at 0x50200000003a thread T0
    #0 0x55a1ade4e1fe in main out_of_bounds.c:6
    #1 0x70fe6c22a1c9 in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #2 0x70fe6c22a28a in __libc_start_main_impl ../csu/libc-start.c:360
    #3 0x55a1ade4e0e4 in _start (out_of_bounds+0x10e4) (BuildId: ec7069e9292b8acada6ab57a772da4c32c98eeeb)

0x50200000003a is located 26 bytes after 16-byte region [0x502000000010,0x502000000020)
allocated by thread T0 here:
    #0 0x70fe6c6fd9c7 in malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:69
    #1 0x55a1ade4e1be in main out_of_bounds.c:5
    #2 0x70fe6c22a1c9 in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #3 0x70fe6c22a28a in __libc_start_main_impl ../csu/libc-start.c:360
    #4 0x55a1ade4e0e4 in _start (out_of_bounds+0x10e4) (BuildId: ec7069e9292b8acada6ab57a772da4c32c98eeeb)

SUMMARY: AddressSanitizer: heap-buffer-overflow out_of_bounds.c:6 in main
Shadow bytes around the buggy address:
  0x501ffffffd80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x501ffffffe00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x501ffffffe80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x501fffffff00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x501fffffff80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x502000000000: fa fa 00 00 fa fa fa[fa]fa fa fa fa fa fa fa fa
  0x502000000080: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x502000000100: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x502000000180: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x502000000200: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x502000000280: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
==2642974==ABORTING
```

Running with the sanitize flag, we get information about the out-of-bounds write.  Highlights:

* heap-buffer-overflow on address 0x50200000003a
* #0 0x55a1ade4e1fe in main out_of_bounds.c:6
* 0x50200000003a is located 26 bytes after 16-byte region [0x502000000010,0x502000000020)

With this, we actually have some actionable information.  But, compiling with the sanitize flag does _not_ identify the missing `free`.

Let's see what Valgrind can find.  Recompile, and then pass it to Valgrind:

```bash
gcc -g -o out_of_bounds out_of_bounds.c

valgrind --leak-check=full ./out_of_bounds
```

Output:

```txt
==2648121== Memcheck, a memory error detector
==2648121== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==2648121== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==2648121== Command: ./out_of_bounds
==2648121== 
==2648121== Invalid write of size 1
==2648121==    at 0x10916B: main (out_of_bounds.c:6)
==2648121==  Address 0x4a8506a is 26 bytes after a block of size 16 in arena "client"
==2648121== 
==2648121== 
==2648121== HEAP SUMMARY:
==2648121==     in use at exit: 16 bytes in 1 blocks
==2648121==   total heap usage: 1 allocs, 0 frees, 16 bytes allocated
==2648121== 
==2648121== 16 bytes in 1 blocks are definitely lost in loss record 1 of 1
==2648121==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==2648121==    by 0x10915E: main (out_of_bounds.c:5)
==2648121== 
==2648121== LEAK SUMMARY:
==2648121==    definitely lost: 16 bytes in 1 blocks
==2648121==    indirectly lost: 0 bytes in 0 blocks
==2648121==      possibly lost: 0 bytes in 0 blocks
==2648121==    still reachable: 0 bytes in 0 blocks
==2648121==         suppressed: 0 bytes in 0 blocks
==2648121== 
==2648121== For lists of detected and suppressed errors, rerun with: -s
==2648121== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
```

Looking at the highlights, we see that both problems have been identified:

Error | Explanation
----- | -----------
Address 0x4a8506a is 26 bytes after a block of size 16 in arena "client" | This refers to the `p[42] = 100` statement. A value is being written outside of the allocated block of memory.
16 bytes in 1 blocks are definitely lost in loss record 1 of 1 | We allocated a block of 16 bytes with `malloc(16)`, but we never freed it.

Fix the code:

```{.c filename="out_of_bounds.c"}
#include <stdlib.h>

int main()
{
    char *p = malloc(16);
    p[8] = 100; /* write within the allocated block */
    free(p);    /* free the allocated block*/

    return 0;
}
```

Compile and check it again:

```bash
gcc -o out_of_bounds out_of_bounds.c

valgrind --leak-check=full ./out_of_bounds
```

Output:

```txt
==2651821== Memcheck, a memory error detector
==2651821== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==2651821== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==2651821== Command: ./out_of_bounds
==2651821== 
==2651821== 
==2651821== HEAP SUMMARY:
==2651821==     in use at exit: 0 bytes in 0 blocks
==2651821==   total heap usage: 1 allocs, 1 frees, 16 bytes allocated
==2651821== 
==2651821== All heap blocks were freed -- no leaks are possible
==2651821== 
==2651821== For lists of detected and suppressed errors, rerun with: -s
==2651821== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

And now, we're good!

# What's Next?

Some additional memory debugging topics you might want to explore:

Topic | Description
----- | -----------
Use after free | This occurs when a program continues to use a memory location after it has been freed or deallocated. This can lead to undefined behavior, crashes, or security vulnerabilities, as the memory may be reallocated for other purposes or may contain invalid data.
Stack/Heap exhaustion | This occurs when the call stack (a special region of memory that stores information about the active subroutines of a computer program) or heap (used for dynamic memory allocation) runs out of available space.
Null pointer dereferences | This occurs when a program attempts to access or manipulate data through a pointer that has not been initialized or has been set to `null`.
Dangling pointers | This describes a pointer that does not point to a valid object of the appropriate type. This situation arises when the object that the pointer was pointing to has been deleted or deallocated, but the pointer itself has not been updated to reflect this change. As a result, the pointer still holds the memory address of the deallocated object, leading to undefined behavior if accessed.

Also, here are some additional tools to consider:

* **Splint**, a tool for statically checking C programs for security vulnerabilities and coding mistakes.
* **Cppcheck** is a static analysis tool that can detect various issues in C/C++ code, including potential memory leaks.
* **Electric Fence**, a linkable library that provides a **malloc** debugger.
* **Dr. Memory**, a memory monitoring tool capable of identifying memory-related programming errors such as accesses of uninitialized memory, accesses to unaddressable memory (including outside of allocated heap units and heap underflow and overflow), accesses to freed memory, double frees, memory leaks, and (on Windows) handle leaks, GDI API usage errors, and accesses to un-reserved thread local storage slots.

# Conclusion

Non-memory safe languages provide a lot of [footguns](https://en.wiktionary.org/wiki/footgun), but with careful planning and judicious use of supporting tools, you can greatly improve the safety of your code.
