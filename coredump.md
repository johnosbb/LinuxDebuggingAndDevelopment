# Debugging with Core Dumps

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

Set the sysroot location in GDB using a local gdbinit file

```sh
echo "set sysroot /home/snuc/debugging-labs/buildroot/output/staging" > gdbinit_local
````

```sh
gdb-multiarch -x gdbinit_local <program_binary> <coredump_file>
```

We can then get a trace of the fault

```sh
(gdb) bt
#0  0xb6e42830 in __pthread_kill_internal () from /home/snuc/debugging-labs/buildroot/output/staging/lib/libc.so.6
#1  0xb6dfef74 in raise () from /home/snuc/debugging-labs/buildroot/output/staging/lib/libc.so.6
#2  0xb6de9434 in abort () from /home/snuc/debugging-labs/buildroot/output/staging/lib/libc.so.6
#3  0xb6e36420 in __libc_message () from /home/snuc/debugging-labs/buildroot/output/staging/lib/libc.so.6
#4  0xb6ec525c in __fortify_fail () from /home/snuc/debugging-labs/buildroot/output/staging/lib/libc.so.6
#5  0xb6ec3ec4 in __chk_fail () from /home/snuc/debugging-labs/buildroot/output/staging/lib/libc.so.6
#6  0xb6ec3974 in __strcpy_chk () from /home/snuc/debugging-labs/buildroot/output/staging/lib/libc.so.6
#7  0x00460a78 in strcpy (__src=0x4722d0 "timegoesbyslowly\n", __dest=0x47335c "") at /home/snuc/debugging-labs/buildroot/output/host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/bits/string_fortified.h:79
#8  insert_name (word=0x4722d0 "timegoesbyslowly\n", index=index@entry=0) at /home/snuc/debugging-labs/sample_code/linked_list/linked_list.c:28
#9  0x00460b3c in load_words (word_file=<optimised out>) at /home/snuc/debugging-labs/sample_code/linked_list/linked_list.c:51
#10 0x004607b0 in main (argc=1, argv=<optimised out>) at /home/snuc/debugging-labs/sample_code/linked_list/linked_list.c:78
(gdb)

```

