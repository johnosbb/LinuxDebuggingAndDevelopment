# Debugging Segmentation Faults


## libSegFault.so

libSegFault.so is a library that will allow to automatically display a backtrace
and more information when your program does generate a segfault.
- It is provided by the glibc and is available for many architectures.
- Can be used without recompilation by preloading it with LD_PRELOAD.
  
for example

```sh
$ LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libSegFault.so ./SegmentingApplication
```

If you are using buildroot then the file can be found in 

```sh
./output/host/usr/arm-buildroot-linux-gnueabihf/sysroot/lib/libSegFault.so
```


## Backtrace

Displaying a backtrace from within your application at the point of the crash, is useful to debug and can be done using the [backtrace](https://man7.org/linux/man-pages/man3/backtrace.3.html) GNU extension

```c
char **backtrace_symbols(void *const *buffer, int size);
```
For the backtrace_symbols() function to work, you'll need a frame pointer and a symbol table (which can be compiled in with -rdynamic). There should be no need to use the -g (debug info) cflag.
