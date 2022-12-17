# Debugging with GDB command line (Part 1)

# Reference

- [GDB on Wikipedia](https://en.wikipedia.org/wiki/Gdb)
- [GDB Project](https://www.sourceware.org/gdb/)

GDB is a GNU debugger which supports Ada, C/C++, Assembly, D, Fortran, GO and RUST. It is mainly used to debug a process by starting it with gdb.

GDB command line debug options
* -g0: Provides no debug information
* -g1: Produce minimal information enough for producing back trace but no information of variables and line numbers.
* -g2: Default debug level (same as -g). Produce symbol, line number required for debugging
* -g3: Extra debug information, includes macro definition.
* -ggdb3: This is like g3, but generates debug information specifically GDB

```sh
aarch64-linux-gnu-gcc -ggdb3 -o helloworld helloworld.c

aarch64-linux-gnu-objdump -h helloworld

----
----
 25 .debug_aranges 00000030  0000000000000000  0000000000000000  000008ee  2**0
                  CONTENTS, READONLY, DEBUGGING
 26 .debug_info   00000452  0000000000000000  0000000000000000  0000091e  2**0
                  CONTENTS, READONLY, DEBUGGING
 27 .debug_abbrev 0000012a  0000000000000000  0000000000000000  00000d70  2**0
                  CONTENTS, READONLY, DEBUGGING
 28 .debug_line   0000079f  0000000000000000  0000000000000000  00000e9a  2**0
                  CONTENTS, READONLY, DEBUGGING
 29 .debug_frame  00000038  0000000000000000  0000000000000000  00001640  2**3
                  CONTENTS, READONLY, DEBUGGING
 30 .debug_str    00043c7a  0000000000000000  0000000000000000  00001678  2**0
                  CONTENTS, READONLY, DEBUGGING
 31 .debug_macro  000154ea  0000000000000000  0000000000000000  000452f2  2**0
                  CONTENTS, READONLY, DEBUGGING
```
Debug information is added into ELF if the -g option is specified. Debug section are kept seperate in the executable from the .text section. This helps by allowing us to run a non debug version of binary on a target system while using the same ELF on the host system for debugging tools.

GDB to work properly, code optimization should be turned off.
- Using -O0 compiler optimization flag
- or enable only GDB compatible optimization flag -Og

## SYSROOT

SYSROOT of a toolchain is the directory containing supporting files such as header files, static libraries, shared libraries etc. 

For a native toolchain sysroot='/'

For a cross toolchain sysroot is usually inside the toolchain directory which can be find using `-print-sysroot`

```sh
aarch64-linux-gnu-gcc -print-sysroot
/home/developer/buildroot/output/host/usr/aarch64-buildroot-linux-gnu/sysroot
```
sysroot tells GDB where to find the debug info and it can be set in GDB using `set sysroot <Toolchain sysroot>`

## Basic GDB Command Line Operation

```sh
  gdb <program_binary_to_debug>
```

GDB can also be attached to running processes using the program PID

```sh
  gdb -p <pid_of_program_binary_to_debug>
```
When using GDB to start a program, the program needs to be run with

```sh
(gdb) run
```
We can manipulate program execution with the following commands:

```gdb
break foobar (b)
# Put a breakpoint at the entry of function foobar()
break foobar.c:42
# Put a breakpoint in foobar.c, line 42
print var, print $reg or print task->files[0].fd (p)
# Print the variable var, the register $reg or a more complicated reference. GDB can also display structures with all their members in a human readable format
info registers (b)
#Display architecture registers
continue (c)
# Continue the execution after a breakpoint
next (n)
# Continue to the next line, stepping over function calls
step (s)
# Continue to the next line, entering into subfunctions
stepi (s)
# Continue to the next instructions
backtrace (bt)
# Display the program stack
```
### Breakpoints

Types of breakpoint
- Normal or software breakpoint: `b <lineno>`
	- GDB adds a trap instruction in the software where break point is defined.
- Temporary breakpoint: `tbreak <lineno>`
	- Stop once and remove the break point automatically.
- Hardware breakpoint: `hbreak <lineno>`
	- When running from flash, adding software breakpoints is not an optimal solution. In that case we can use hardware breakpoint which are dependent on CPU architecture. When the instruction pointer matches the hardware break point mask it stop the execution and bring back to debugger.

Conditional breakpoint: Stop debugger at breakpoint if condition is matched. It slows down the execution speed so not recommended to debug race conditions.
`break <lineno> if <condition is true>`

List breakpoints: `info break`

Delete breakpoint: `delete break <NUM>`

Execute command after hitting the breakpoint
```
(gdb) After b main
(gdb) commands
Type commands to execute after breakpoint and use "End" keyword to end command list
```
### Watchpoints

Watchpoints break the program when a selected varaible's value changes

Examples:
- watch -l <address/symbol>
- rwatch <a/s> 				Stops if the address is read
- watch <a/s> thread 3 			Stops if thread 3 modifies
- watch <a/s> if <a/s> > 5		Stops when the contents > 5 (Uses Hardware breakpoint)

Shows the old and new value when the watchpoint is hit.

Conditional Watchpoints: `watch <a/s> if <condition is true>`

### File command

GDB provides an option of loading the program and files through `file <filename>` command from GDB prompt. This comes handy while debugging Kernel code through kgdb. Kernel code doesn't contains dynamically loaded kernel module. Using file command we can load the modules in the kernel while debugging it through kgdb.

- Load the program using file command.
- Provide the arguments using `set args` command.
- Check the argument using `show args` command.
- run it using `run` command.

In case if GDB is attached to a running process using `gdb -p <PID>`, we can load the executable ( with -g compiled) and symbol table using file command to debug the attached process.

### List command

List command is used to list source code. Following options are possible with list command 
- LINENUM
- FILE:LINENUM
- FUNCION
- FILE:FUNCTION
- *ADDRESS

`set output-radix 16` would set the display radix to hexadecimal.

### Call command

Once the program is loaded, using call command we can call a function from GDB command line. 
`call function(a,b)`

### Shell command

Using `shell <cmd>` GDB will shell out and run the provided OS command.  

### Thread Debugging

`info threads` Shows active thread IDs
`thread n` Select a thread by ID
`break <lineno> thread <id>` Restrict breakpoint to a particular thread. Other thread doesn't hit the same breakpoint.
`thread apply all bt` Show backtrace of all the threads.

## Printing in GDB

Use `print <EXP>` to print any value from current stack, frame, globals and entire file. GDB supports printing the past values of a variable by appending it using $ symbol.

Example: $NUM is a value previous to currently stored in NUM. Simillarly, $$NUM is value previously stored before $NUM and so on.

The @ symbol is a binary operator used in objects with consecutive data.

We can use x option to print memory at the address in a given size.

Example: x /db &test -> Prints memory of test as a decimal byte.

## Signals in GDB

GDB is built on ptrace, when the process gets a signal it gets suspended and GDB is notified about the singal.

```
info signal

info handle -> Handle change the way how signal is handeled in GDB
	- nostop: do not stop the program but print the signal occured
	- stop: stop program when signal occurs
	- print: print a message when signal occur
	- noprint: do not mention when a signal occur
	- pass: Allow the program to see the signal so it can be handeled
	- nopass: do not pass the signal to your program

handle signal keyword -> Assign a handle to a signal.
	- Example: handle SIGILL nostop

signal SEGSEGV -> Deliver SEGV signal to current program
```

## Using the multi option with gdb

You can start gdbserver without supplying an initial command to run or process ID to attach. To do this, use the --multi command line option. Then you can connect using target extended-remote and start the program you want to debug (using the run command). Note that the conditions under which gdbserver terminates depend on how GDB connects to it (target remote or target extended-remote). The --multi option to gdbserver has no influence on that.

### The run command

With target remote mode: The run command is not supported. Once a connection has been established, you can use all the usual GDB commands to examine and change data. The remote program is already running, so you can use commands like step and continue.

With target extended-remote mode: The run command is supported. The run command uses the value set by set remote exec-file (see set remote exec-file) to select the program to run. Command line arguments are supported, except for wildcard expansion and I/O redirection (see Arguments).

If you specify the program to debug on the command line, then the run command is not required to start execution, and you can resume using commands like step and continue as with target remote mode.

### Attaching

With target remote mode: The GDB command attach is not supported. To attach to a running program using gdbserver, you must use the --attach option (see Running gdbserver).

With target extended-remote mode: To attach to a running program, you may use the attach command after the connection has been established. If you are using gdbserver, you may also invoke gdbserver using the --attach option (see Running gdbserver).

Some remote targets allow GDB to determine the executable file running in the process the debugger is attaching to. In such a case, GDB uses the value of exec-file-mismatch to handle a possible mismatch between the executable file name running in the process and the name of the current exec-file loaded by GDB (see set exec-file-mismatch).
