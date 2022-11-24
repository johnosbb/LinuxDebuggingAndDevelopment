# GDB and Python Integration

GDB features a python integration; this allows us to script some debugging operations by executing python under GDB, a python module named gdb is available and all the
GDB specific classes are accessible under this module. The module allows customisation of commands, breakpoint, printers etc.
It can be used by the kernel to create new commands with the python GDB scripts and these scripts allow full control and observability over the debugged program, including controlling execution, adding breakpoints, watchpoints, accessing the process memory, frames, symbols, etc

- [GDB and Python](https://sourceware.org/gdb/onlinedocs/gdb/Python.html)


```python

class PrintOpenFD(gdb.FinishBreakpoint):
def __init__(self, file):
self.file = file
super(PrintOpenFD, self).__init__()
def stop (self):
print ("---> File " + self.file + " opened with fd " + str(self.return_value))
return False
class PrintOpen(gdb.Breakpoint):
def stop(self):
PrintOpenFD(gdb.parse_and_eval("file").string())
return False
class TraceFDs (gdb.Command):
def __init__(self):
super(TraceFDs, self).__init__("tracefds", gdb.COMMAND_USER)
def invoke(self, arg, from_tty):
print("Hooking open() with custom breakpoint")
PrintOpen("open")
TraceFDs()


```
