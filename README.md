# Linux Debugging And Development
Embedded Linux Debugging And Development

## References

- [Debugging with gdb - Chris Simmonds](https://elinux.org/images/0/01/Debugging-with-gdb-csimmonds-elce-2020.pdf)
- [Debugging with gdb - Youtube, Chris Simmonds](https://www.youtube.com/watch?v=JGhAgd2a_Ck)


## Debugging

- [GDB Command line Debugging](gdb.md)
- [Debugging with Core Dumps](coredump.md)
- [Comand Line Options for Segmentation Faults](segmentation.md)


### Remote Debugging Options

- [Remote Debugging with VSCode](vscode.md)
- [Remote Debugging with Eclipse](eclipse.md)
- [Remote Debugging with GDB Command Line Tools](gdb_remote.md)



### Note on using set sysroot

The default value for the set sysroot variable depends on your toolchain. If your GDB binary was compiled with the --sysroot argument, you won't need to run the set sysroot command - the sysroot will be automatically set to the location specified during compilation. Otherwise the default value will be "" and you might need to set it manually if you are debugging remote processes.

You can check if the compiler knows the location of sysroot with:

```sh
/home/snuc/debugging-labs/buildroot/output/host/usr/bin/arm-linux-gcc  --print-sysroot
# output
/home/snuc/debugging-labs/buildroot/output/host/arm-buildroot-linux-gnueabihf/sysroot
```

#### Sysroot in Buildroot

In buildroot the staging area is a symbolic link to the sysroot: /home/snuc/debugging-labs/buildroot/output/host/arm-buildroot-linux-gnueabihf/sysroot

```sh
/home/snuc/debugging-labs/buildroot/output/staging -> /home/snuc/debugging-labs/buildroot/output/host/arm-buildroot-linux-gnueabihf/sysroot
```

### How to check if Symbols are present in binary

```sh
readelf --debug-dump=decodedline $1
```


### Dealing with Optimisation when Debugging

Optimisation can make debugging with gdb difficult. There are a number of options:
1. Turn off optimisation completely by using the -O0 flag
2. Enable gdb friendly optimisation by using the -Og flag
3. Annotate a specific function with: 
```c
__attribute__((optimize("O0")))
```


# Assembly Language Tool Online

- [Online Tool](https://godbolt.org/)


