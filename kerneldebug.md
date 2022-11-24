
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
- Example: dev_info(&pdev->dev, "in probe\n"); This will produce [ 25.884873] serial 481a8000.serial: in probe


## Enabling Debug

The pr_debug() and dev_dbg() macros are enabled when the driver is compiled with DEBUG defined. This can be done by #define DEBUG at the beginning of the driver, or using ccflags-$(CONFIG_DRIVER) += -DDEBUG in the Makefile.

When the kernel is compiled with CONFIG_DYNAMIC_DEBUG, then these messages can [dynamically](https://www.kernel.org/doc/Documentation/admin-guide/dynamic-debug-howto.rst) be enabled on a per-file, per-module or per-message basis. 

If we use CONFIG_DYNAMIC_DEBUG we can enable on-demand debugging with sysctrl interface by echoing the appropriate strings to /sys/kernel/debug/dynamic_debug/control


