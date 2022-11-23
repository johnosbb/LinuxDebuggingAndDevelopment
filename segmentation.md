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

When an application crashes due to a segmentation fault and the application was not under control of a debugger, we get no information about the crash; however, Linux can generate a core file that contains the image of the application memory at the moment of the crash, and gdb can use this core file to let us analyze the state of the crashed application

To enable core dumping on the target we can use the following command:

To enable core dumps 

```sh
ulimit -c unlimited
```


If required the output name for the coredump file can be modified by writing to:

```sh
/proc/sys/kernel/core_pattern.
```

Then, run the program normally:

```sh
$ ./program_to_debug
```

It will produce a segmentation fault (core dumped) as it crashes. Copy this file to your host system then use gdb-multiarch.

```sh
gdb-multiarch <program_binary> <coredump_file>
```

Set the sysroot location in GDB

```sh
set sysroot /home/snuc/debugging-labs/buildroot/output/staging

```


