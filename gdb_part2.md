# Debugging with GDB command line (Part 2)

## Stack frames and backtrace

Each funtion has a stack frame which contains its local (auto) variable. The `bt` command dumps the stack frames and the call chain of the program. 

We can use `info local` to show all local variables in a corresponding stack frame and using `frame N` we can change the current stack frame.

## Debugging with library codes

By default GDB searches the source code in 

	- $cdir: The compile directory (Encoded in ELF)
	- $cwd: current working directory

The search path can be extended using `dir <Additional path>` command.

While using build system like buildroot, debug is available in sysroot. GDB can replace the embedded path with the new path appended with sysroot. 

`(gdb) set substitue-path /usr/src/debug <SYSROOT PATH>/usr/src/debug`

## Defining your own commands

GDB has the ability to define custom commands and scripts using `define <name>` command

```sh
define bmain
break main
info break
end
```
You can then be run just like any other gdb command:

```sh
(gdb) bmain
Breakpoint 1 at 0x400516: file helloworld.c, line 7.
Num Type Disp Enb Address What
1 breakpoint keep y 0x00400516 in main at helloworld.c:7
```
This is useful for sequences that you use many times and you can put these commands into command files for different types of debugging.

### GDB scripts
GDB will execute all the commands defined in a .gdbinit file at the load time of GDB. Some useful load time command are defined below
```
**Content inside .gdbinit file**
set history save on
set pretty print on
set pagination off
set confirm off

$ gdb-multiarch -x .gdbinit <Executable Binary>
```
## Graphical mode

GDB supports text based graphic mode, which presents the source code in a separate window along with GDB window.

### Terminal User Interface (TUI) Mode

TUI [Command References](https://sourceware.org/gdb/onlinedocs/gdb/TUI-Commands.html)

```sh
gdb-multiarch -tui <Executable Binary>
Toggle ON and OFF from GDB using ctrl-x a
```
### cgdb Mode

This is similar to TUI mode but more enhanced in terms of source code representation. 

CGDB [Reference](https://cgdb.github.io/)

Install
```
$ git clone git://github.com/cgdb/cgdb.git
$ cd cgdb
$ ./autogen.sh
$ ./configure --prefix=/usr/local
$ make
$ sudo make install
```

Execute
`cgdb -d gdb-multiarch <Executable Binary>`
