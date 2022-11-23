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
__LD_LIBRARY_PATH__ is an environmental variable used in Linux/UNIX Systems. It is used to tell dynamic link loaders where to look for shared libraries for specific applications. It will designate additionnal path to be searched for library loading. By default, the dynamic loader will search for some paths like /etc/ld.so.cache, /lib and /usr/lib. This can be extended by adding path int /etc/ld.so.conf and running ldconfig to update the dynamic loader cache (/etc/ld.so.cache). Compiling with the [rpath](https://en.wikipedia.org/wiki/Rpath) option also allows you to specify additional search directories.


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



## strace

strace is a diagnostic, debugging and instructional userspace utility for Linux. It is used to monitor and tamper with interactions between processes and the Linux kernel, which include system calls, signal deliveries, and changes of process state.

System administrators, diagnosticians and trouble-shooters will find it invaluable for solving problems with programs for which the source is not readily available since they do not need to be recompiled in order to trace them.

- [System call tracer](https://strace.io)
- Available on all GNU/Linux systems
- Can be built by your cross-compiling toolchain generator or by a build system.
- Allows to see what any of your processes is doing: accessing files, allocating memory... Often sufficient to find simple bugs.

### Usage:

- strace <command> (starting a new process)
- strace -f <command> (follow child processes too)
- strace -p <pid> (tracing an existing process)
- strace -c <command> (time statistics per system call)

 example
 
 ```sh
 strace ./binary_program_to_trace
 ```
 
 __Note__: strace will trace the calls of the dynamic linker as the program launches, so exclude these from your analysis
 
 ```sh
execve("./binary_program_to_trace", ["./binary_program_to_trace"], 0xbedd8e30 /* 12 vars */) = 0
brk(NULL)                               = 0x4c2000
mmap2(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb6f2f000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
...
kill(0, SIGALRM)                        = 0
--- SIGALRM {si_signo=SIGALRM, si_code=SI_USER, si_pid=187, si_uid=0} ---
sigreturn({mask=[]})   <-------------------------- dynamic linker code ends here
openat(AT_FDCWD, "/etc/init.d/S20urandom", O_RDONLY|O_LARGEFILE) = 5 <-------------------------- actual program calls start here on this line
openat(AT_FDCWD, "/etc/config", O_RDONLY|O_LARGEFILE) = -1 ENOENT (No such file or directory)
close(4)                                = 0
kill(1, SIGABRT)                        = 0
getpid()                                = 187
statx(AT_FDCWD, "/lib/libdl.so.2", AT_STATX_SYNC_AS_STAT|AT_SYMLINK_NOFOLLOW|AT_NO_AUTOMOUNT, STATX_BASIC_STATS, {stx_mask=STATX_BASIC_STATS|STATX_MNT_ID, stx_attributes=0, stx_mode=S_IFREG|0755, stx_size=7364, 
...}) = 0
exit_group(0)                           = ?
+++ exited with 0 +++
 
 ```
 
 
## Hooking symbols and overloading library calls

In order to do some more complex library call hooks, one can use the LD_PRELOAD environment variable. LD_PRELOAD is used to specify a shared library that will be loaded before any other library by the dynamic loader. This facility allows us to intercept all library calls by preloading another library with symbols that have the same name and signature.
This is the technique used by libraries like libsegfault and libefence. The technique works for both C and C++.

Example usage

```sh
LD_PRELOAD=my_overload_library.so ./application_to_overload
```


