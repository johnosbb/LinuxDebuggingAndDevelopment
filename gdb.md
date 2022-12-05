# Debugging with GDB command line

# Reference

- [GDB on Wikipedia](https://en.wikipedia.org/wiki/Gdb)
- [GDB Project](https://www.sourceware.org/gdb/)

## Basic Command Line Operation

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
debug information added into ELF if -g option is specified. Debug section are kept seperate in the executable from the .text section. That helps running a non debug version of binary on a target system and on the host system using debug version of smae ELF,  code can be debugged.

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

## Defining your own commands

You can define a new command defining it:

```sh
define bmain
break main
info break
end

```

You can then run it just like any other gdb command:

```sh
(gdb) bmain
Breakpoint 1 at 0x400516: file helloworld.c, line 7.
Num Type Disp Enb Address What
1 breakpoint keep y 0x00400516 in main at helloworld.c:7
```
This is useful for sequences that you use many times and you can put these commands into command files for different types of debugging.


