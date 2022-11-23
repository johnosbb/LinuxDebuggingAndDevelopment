# Tracing on Linux Systems


## ltrace

ltrace is a tool to trace shared library calls used by a program and all the signals it receives. It is a useful complement to strace, which shows only system calls and not the system calls made by linked libraries.
It works even without sources and provides filtering of library calls with regular expressions, or just by a list of function names.
- Also offers a summary with its -c optio which counts time and calls for each library call and reports a  summary on program exit.
- [Manual page](https://linux.die.net/man/1/ltrace)
- Does not work with uClibc 



If the library you are tracing is not in the standard library locations then we may call 
```sh
export LD_LIBRARY_PATH=$PWD
```
__LD_LIBRARY_PATH__ is an environmental variable used in Linux/UNIX Systems. It is used to tell dynamic link loaders where to look for shared libraries for specific applications.


Example usage for an application (called app) with shared library linkage:

```sh
ltrace ./app
__libc_start_main([ "./app" ] <unfinished ...>
al_init()                                        = 0
al_alloc_context()                               = 0x422190
al_build_user_list(0x422190)                     = <void>
al_authent_user(0x422190, 'u', 'm')              = 1
puts("Failed to authenticate user"Failed to authenticate user
)              = 28
__cxa_finalize(0x421004, 0, 56, 0x1094c)         = 0xb6f43a84
+++ exited (status 1) +++
```


```sh

ltrace -c ./app
Failed to authenticate user
% time     seconds  usecs/call     calls      function
------ ----------- ----------- --------- --------------------
 54.76    0.070862       70862         1 __libc_start_main
 24.85    0.032155       32155         1 puts
  6.03    0.007803        7803         1 al_alloc_context
  3.35    0.004336        4336         1 __cxa_finalize
  3.01    0.003891        3891         1 al_init
  2.94    0.003804        3804         1 al_build_user_list
  2.67    0.003457        3457         1 al_authent_user
  2.39    0.003097        3097         1 exit_group
------ ----------- ----------- --------- --------------------
100.00    0.129405                     8 total
```

## Hooking symbols and overloading library calls

In order to do some more complex library call hooks, one can use the LD_PRELOAD environment variable. LD_PRELOAD is used to specify a shared library that will be loaded before any other library by the dynamic loader. This facility allows us to intercept all library calls by preloading another library with symbols that have the same name and signature.
This is the technique used by libraries like libsegfault and libefence. The technique works for both C and C++.

