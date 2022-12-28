
# Debugging the Kernel

Kernel features some very useful tools for debugging. These tools are builtin the kernel since their activation often select instrumentation code for debugging. These include:

- Erroneous memory accesses debugging tools (KASAN, Kmemleak, KFENCE)
- Undefined behavior code debugging (UBSAN). For example shifting a 32 bit value beyond 32 bits
- Locking errors analysis (lockdep)
- All the debug features are located under the Kernel hacking -> Kernel debugging menuconfig entry. CONFIG_DEBUG_KERNEL should be set to ”y” to enable other debug options.


# Options for debugging using printing
Three APIs are available
- The old printk(), no longer recommended for new debugging messages.
- The pr_*() family of functions: pr_emerg(), pr_alert(), pr_crit(), pr_err(),pr_warning(), pr_notice(), pr_info(), pr_cont() and the special pr_debug(). These are defined in include/linux/printk.h
- print_hex_dump_debug(): useful to dump a buffer with hexdump like display.

The kernel defines much more format specifiers than the standard printf() existing ones.
-  %p: Display the hashed value of pointer by default for security reasons
- %px: Always display the address of a pointer (use carefully on non-sensitive
addresses).
- %pK: Display hashed pointer value, zeros or the pointer address depending on kptr_restrict sysctl value. [see here](https://sysctl-explorer.net/kernel/kptr_restrict/)
- %pOF: Device-tree node format specifier.
- %pr: Resource structure format specifier.
- %pa: Physical address display (work on all architectures 32/64 bits)
-  See core-api/printk-formats for an exhaustive list of supported format specifiers

## Driver Debugging
There are debug facilities to be used in drivers integrated with the Linux device model

- The dev_*() family of functions: dev_emerg(), dev_alert(), dev_crit(),dev_err(), dev_warn(), dev_notice(), dev_info() and the special dev_dbg()
- They take a pointer to struct device as first argument, and then a format string with arguments and are defined in include/linux/dev_printk.h
- Example: dev_info(&pdev->dev, "in probe\n"); This will produce:

```sh
   [ 25.884873] serial 481a8000.serial: in probe
````

## Enabling Debug

The pr_debug() and dev_dbg() macros are enabled when the driver is compiled with DEBUG defined. This can be done by #define DEBUG at the beginning of the driver, or using ccflags-$(CONFIG_DRIVER) += -DDEBUG in the Makefile.

When the kernel is compiled with CONFIG_DYNAMIC_DEBUG, then these messages can [dynamically](https://www.kernel.org/doc/Documentation/admin-guide/dynamic-debug-howto.rst) be enabled on a per-file, per-module or per-message basis. 

If we use CONFIG_DYNAMIC_DEBUG we can enable on-demand debugging with sysctrl interface by echoing the appropriate strings to /sys/kernel/debug/dynamic_debug/control.
This has a dependency on the ‘debugfs’ filesystem. Thus, you must first mount the debugfs filesystem, in order to make use of this feature. 

```sh
mount -t debugfs none /sys/kernel/debug/
```

You can view the currently configured behaviour of all the debug statements via:

```sh
echo 'file svcsock.c line 1603 +p' > /sys/kernel/debug/dynamic_debug/control
```

[See also the Dynamic Debug How to](https://www.kernel.org/doc/html/v5.0/admin-guide/dynamic-debug-howto.html)

## Files that support Kernel Debugging

Compiling the kernel generates a number of files that can assist in debugging the kernel:
- [vmlinux](https://en.wikipedia.org/wiki/Vmlinux): This is a statically linked executable file that contains the Linux kernel in one of the object file formats supported by Linux Kernel (ELF for example). It is used for debug and to derivate other images
- [system.map](https://en.wikipedia.org/wiki/System.map#:~:text=In%20Linux%2C%20the%20System.,the%20name%20of%20a%20function.): A symbol table is a look-up between symbol names and their addresses in memory. A symbol name may be the name of a variable or the name of a function. The System.map is required when the address of a symbol name, or the symbol name of an address, is needed. It is especially useful for debugging kernel panics and kernel oopses. Contain a list of addresses/symbols that are embedded in the kernel
- vmlinux-gdb.py: Scripts to assist kernel debugging with GDB


## Debugging the Kernel

The kernel can crash for many reasons:
- Memory access errors (NULL pointer, out of bounds access, etc)
- Voluntarily panicking on error detection (using panic())
- Kernel incorrect execution mode (sleeping in atomic context)
- Deadlocks detected by the kernel (Soft lockup/locking problem)

When crashing, the kernel will display a message on the console that is called a ”Kernel oops”

## Using a Secondary Dump-Capture Kernel

Kexec is a mechanism of the Linux kernel that allows booting of a new kernel from the currently running one by skipping the bootloader stage and hardware initialization phase performed by the system firmware (BIOS or UEFI), and directly loading the new kernel into main memory and executing it immediately. 
This allows us dump a perfect copy of the crashed kernel (memory, registers, etc) which can be then debugged using gdb or crash.

We can use use a simple buildroot image with a builtin initramfs to create the dump-capture kernel.
**Note**: Support for allowing only signed kernels to be booted through kexec was merged into version 3.17 of the Linux kernel mainline.

### Building the Dump-Capture Kernel

```sh
cd /home/<user>/debugging-labs/buildroot
make O=build_kexec stm32mp157a_dk1_debugging_kexec_defconfig
cd build_kexec
make -j12
```

If we are using a nfs mount we copy the dump-capture kernel and the device tree into a defined location in the nfs root

```sh
mkdir /home/<user>/debugging-labs/nfsroot/root/kexec
cp build_kexec/images/zImage /home/<user>/debugging-labs/nfsroot/root/kexec
cp build_kexec/images/stm32mp157a-dk1.dtb /home/<user>/debugging-labs/nfsroot/root/kexec
```

We need to tell the main kernel about the dump-capture kernel by adding the following detail to the command line. Intrerrupt the uboot bootup process and change the command line as shown below:

oops=panic will cause the kernel to generate an oops on panic and crashkernel=60M reserve 60 MB for the kernel in memory.

```sh
env edit bootargs
<existing bootargs> crashkernel=60M oops=panic
boot
```

We can load the dump-capture kernel into memory with

```sh
kexec --type zImage -p /root/kexec/zImage --dtb=/root/kexec/stm32mp157a-dk1.dtb
--command-line="console=ttySTM0,115200n8 maxcpus=1 reset_devices"
```

We then generate a kenel oops with

```sh
watchdog -T 10 -t 5 /dev/watchdog0
```

![image](https://user-images.githubusercontent.com/12407183/209828387-fb42842d-cbba-43cb-acd3-96afa122557d.png)

At the end of the crash sequence we can see the dump-capture kernel loading:

![image](https://user-images.githubusercontent.com/12407183/209828676-11ab45b4-ed79-4565-a72e-fb98d54cf1db.png)


We can log into this clean kernel and set the ethernet interface

```sh
# ifconfig
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

# ifconfig eth0 192.168.0.101
[  247.860640] stm32-dwmac 5800a000.ethernet eth0: PHY [stmmac-0:00] driver [Generic PHY] (irq=POLL)
[  247.868450] stm32-dwmac 5800a000.ethernet eth0: Register MEM_TYPE_PAGE_POOL RxQ-0
[  248.884129] stm32-dwmac 5800a000.ethernet: Failed to reset the dma
[  248.888877] stm32-dwmac 5800a000.ethernet eth0: stmmac_hw_setup: DMA engine initialization failed
[  248.898249] stm32-dwmac 5800a000.ethernet eth0: stmmac_open: Hw setup failed
ifconfig: SIOCSIFFLAGS: Connection timed out
# ifconfig eth0 192.168.0.101
[  265.216671] stm32-dwmac 5800a000.ethernet eth0: PHY [stmmac-0:00] driver [Generic PHY] (irq=POLL)
[  265.224447] stm32-dwmac 5800a000.ethernet eth0: Register MEM_TYPE_PAGE_POOL RxQ-0
[  265.242953] dwmac4: Master AXI performs any burst length
[  265.246925] stm32-dwmac 5800a000.ethernet eth0: No Safety Features support found
[  265.254621] stm32-dwmac 5800a000.ethernet eth0: IEEE 1588-2008 Advanced Timestamp supported
[  265.262932] stm32-dwmac 5800a000.ethernet eth0: registered PTP clock
[  265.270106] stm32-dwmac 5800a000.ethernet eth0: configuring for phy/rgmii-id link mode
# [  268.406915] stm32-dwmac 5800a000.ethernet eth0: Link is Up - 1Gbps/Full - flow control rx/tx
[  268.413989] IPv6: ADDRCONF(NETDEV_CHANGE): eth0: link becomes ready

```

**Note**: We use 192.168.0.101 to avoid cloberring ssh know_hosts file for the 192.168.0.100 entry and we may also have to run the command to set the IP address twice  because of some init issues after kexec boot.

We can retrieve the crashed core with the following command run on the host machine

```sh
 scp root@192.168.0.101:/proc/vmcore ./vmcore
```

![image](https://user-images.githubusercontent.com/12407183/209829627-8354782f-8352-42d6-b94a-aaa9c8af6db7.png)


We can then analyse the recovered core using the [crash utility](crashutility.md)
