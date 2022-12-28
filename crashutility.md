
# Crash Utility

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

