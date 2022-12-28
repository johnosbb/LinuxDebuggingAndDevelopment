
# Crash Utility

## References

- [man page](https://man7.org/linux/man-pages/man8/crash.8.html#:~:text=Crash%20is%20a%20tool%20for,xendump%20kvmdump%20or%20VMware%20facilities.)

## Building the Crash Program for ARM

Install the dependecies

```sh
sudo apt install gcc-multilib g++-multilib lib32z1-dev lib32ncurses5-dev texinfo flex bison
```

Download the crash utility

```sh
git clone https://github.com/crash-utility/crash.git
cd ./crash-utility
make target=ARM #to build for our target
```

## Anaylsing the Core

We can load a vmcore with

```sh
sudo chown snuc:snuc ../buildroot-dump-capture/vmcore

./crash ../buildroot-sdcard/output/build/linux-5.13/vmlinux ../buildroot-dump-capture/vmcore 

crash 8.0.2
Copyright (C) 2002-2022  Red Hat, Inc.
Copyright (C) 2004, 2005, 2006, 2010  IBM Corporation
Copyright (C) 1999-2006  Hewlett-Packard Co
Copyright (C) 2005, 2006, 2011, 2012  Fujitsu Limited
Copyright (C) 2006, 2007  VA Linux Systems Japan K.K.
Copyright (C) 2005, 2011, 2020-2022  NEC Corporation
Copyright (C) 1999, 2002, 2007  Silicon Graphics, Inc.
Copyright (C) 1999, 2000, 2001, 2002  Mission Critical Linux, Inc.
Copyright (C) 2015, 2021  VMware, Inc.
This program is free software, covered by the GNU General Public License,
and you are welcome to change it and/or distribute copies of it under
certain conditions.  Enter "help copying" to see the conditions.
This program has absolutely no warranty.  Enter "help warranty" for details.
 
GNU gdb (GDB) 10.2
Copyright (C) 2021 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "--host=x86_64-pc-linux-gnu --target=arm-elf-linux".
Type "show configuration" for configuration details.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...

      KERNEL: ../buildroot-sdcard/output/build/linux-5.13/vmlinux
    DUMPFILE: ../buildroot-dump-capture/vmcore
        CPUS: 2 [OFFLINE: 1]
        DATE: Sat Jan  1 17:46:03 GMT 2000
      UPTIME: 00:04:45
LOAD AVERAGE: 3.31, 2.06, 0.90
       TASKS: 75
    NODENAME: buildroot
     RELEASE: 5.13.0
     VERSION: #1 SMP PREEMPT Mon Dec 26 19:55:57 GMT 2022
     MACHINE: armv7l  (unknown Mhz)
      MEMORY: 512 MB
       PANIC: "Unable to handle kernel NULL pointer dereference at virtual address 00000070"
         PID: 135
     COMMAND: "watchdog"
        TASK: c3eb8e40  [THREAD_INFO: c3e0a000]
         CPU: 1
       STATE: TASK_RUNNING (PANIC)

crash> 
```


We can use backtrace to identify the point of the crash:

```sh
crash> bt
PID: 135      TASK: c3eb8e40  CPU: 1    COMMAND: "watchdog"
 #0 [<c07c3830>] (stm32_iwdg_start) from [<c07c2c74>]
 #1 [<c07c2be0>] (watchdog_start) from [<c07c2db4>]
 #2 [<c07c2d34>] (watchdog_open) from [<c030ace0>]
 #3 [<c030ac14>] (chrdev_open) from [<c02ff6bc>]
 #4 [<c02ff57c>] (do_dentry_open) from [<c03015e0>]
 #5 [<c03015a8>] (vfs_open) from [<c0318088>]
 #6 [<c0317540>] (path_openat) from [<c03197e0>]
 #7 [<c0319754>] (do_filp_open) from [<c03018bc>]
 #8 [<c0301810>] (do_sys_openat2) from [<c0301d5c>]
 #9 [<c0301cb4>] (sys_openat) from [<c0100060>]
    pc : [<b6eac018>]    lr : [<0048e2c8>]    psr: 600f0010
    sp : be96ec48  ip : 00562c44  fp : 00000000
    r10: be96ee60  r9 : b6f3ce60  r8 : 005618a4
    r7 : 00000142  r6 : be96ee44  r5 : b6f9c1a0  r4 : 00000000
    r3 : 00000000  r2 : 00020001  r1 : be96ef58  r0 : ffffff9c
    Flags: nZCv  IRQs on  FIQs on  Mode USER_32  ISA ARM
crash> 


```
