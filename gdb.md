# Debugging with GDB command line


## Using the multi option with gdb





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


