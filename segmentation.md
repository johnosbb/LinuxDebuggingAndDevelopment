# Debugging Segmentation Faults


## libSegFault.so

libSegFault.so is a library that will allow to automatically display a backtrace
and more information when your program does generate a segfault.
- It is provided by the glibc and is available for many architectures.
- Can be used without recompilation by preloading it with LD_PRELOAD.
  
for example

```sh
$ LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libSegFault.so ./segfault
```

If you are using buildroot then the file can be found in 

```sh
./output/host/usr/arm-buildroot-linux-gnueabihf/sysroot/lib/libSegFault.so
```
