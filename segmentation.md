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


## Using Core Dumps

To enable core dumps 

```sh
ulimit -c unlimited
```

Then, run the program normally:

```sh
$ ./program_to_debug
```

It will produce a segmentation fault (core dumped) as it crashes. Copy this file to your host system then use gdb-multiarch.

```sh
gdb-multiarch <program_binary> <coredump_file>
```

