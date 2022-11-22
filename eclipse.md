# Remote Debugging with Eclipse

## Eclipse

### Eclipse Debugging
- Create a new project for the code you wish to debug in Eclipse -> select File->New->Makefile
Project With Existing Code
- Set up a remote debug session with gdb, go to Run and select Debug Configurations
- Under C++ Remote Application create a new configuration using the new launch configuration icon
- On the main tab on the right browse to the project directory of the code you want to debug
- Under C++ Application select the actual executable that you want to debug
- Under connection, create a new SSH connection with the IP address of the board you want
to debug.
- Set the Remote Absolute Path to the path on the target of the application you wish to debug, for example, /usr/bin/myapplication
- Select 'Skip download to target'
- Now move to the arguments tab and enter any arguments the binary requires
- Now move to the debugger tab and enter the following:
- In the GDB Debugger textbox enter the path to GDB: /home/developer/buildroot2017.02.4/output/host/usr/bin/aarch64-linux-gdb or /home/developer/buildroot2017.02.4/output/host/usr/bin/arm-linux-gnueabihf-gdb depending on the tool-chain
- In the GDB Init textbox enter the path to the appropriate encoder or decoder GDB init files: /home/developer/buildroot2017.02.4/board/gdbinit for example
- Now go to the shared libraries tab, enter the paths to all of the shared libraries, typically these are in :  
    - /home/developer/buildroot2017.02.4/output/staging/usr/lib
    - /home/developer//buildroot2017.02.4/output/staging//lib
    - /home/developer//buildroot2017.02.4/output/staging/usr/lib/freerdp
- Apply all of the settings


### Sample gdbinit file

```sh
set sysroot /home/developer/buildroot-2017.02.4/output/staging
set solib-search-path /home/developer/buildroot/buildroot-2017.02.4/output/staging/usr/lib

```
Note: Recent security changes in GDB require us to add the line: add-auto-load-safe-path /home/myname/myproject/.gdbinit to our gdbinit file

```sh
add-auto-load-safe-path /home/developer/myproject/.gdbinit
set sysroot /home/developer/buildroot-2017.02.4/output/staging
set solib-search-path /home/developer/buildroot/buildroot-2017.02.4/output/staging/usr/lib

```
