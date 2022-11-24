# Prevention Strategies

Some good practices can allow you to save time before even needing to use a debugger. Compiler are now smart enough to detect a wide range of errors at compile-time
using warnings so using -Werror -Wall -Wextra is recommended if possible to catch errors as early as possible.

Compilers now offer static analysis capabilities, for example, GCC allows to do so using the -fanalyzer flag.

## Static Analysis Tools

### References
- [Static Analysis for C](https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis#C,_C++)

### CPPCheck

Cppcheck is a static analysis tool for C/C++ code. It provides unique code analysis to detect bugs and focuses on detecting undefined behaviour and dangerous coding constructs. The goal is to have very few false positives. Cppcheck is designed to be able to analyze your C/C++ code even if it has non-standard syntax (common in embedded projects).

- [CPPCheck](https://cppcheck.sourceforge.io/)


