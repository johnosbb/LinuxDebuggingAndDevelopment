# Linux Debugging And Development
Linux Debugging And Development


## Remote Debugging
### VSCode
- [References: Setting up Remote Debugging](https://opencoursehub.cs.sfu.ca/bfraser/grav-cms/cmpt433/guides/files/DebuggingGuide.pdf)
- Install the native debugger
- ![image](https://user-images.githubusercontent.com/12407183/203298495-6a8a42cc-7903-4c2b-8356-4763b9bd44ab.png)
- Create a debug configuration launch.json file:
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "gdb",
            "request": "attach",
            "name": "Attach to gdbserver",
            "executable": "/usr/bin/linked_list",
            "target": "192.168.0.100:2000",
            "remote": true,
            "cwd": "${workspaceRoot}", 
            "gdbpath": "/usr/bin/gdb-multiarch",
            "autorun": [
                    "set sysroot /home/snuc/debugging-labs/buildroot/output/staging"
                ]
        }
    ]
}
```
- Compile the code with debug symbols:
```sh
/home/snuc/debugging-labs/buildroot/output/host/usr/bin/arm-linux-gcc  linked_list.c -g -Wall -Werror -o linked_list
```
- Run gdbserver on the target:
```sh
gdbserver localhost:2000 /usr/bin/linked_list
```
### Eclipse

#### Eclipse Debugging
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


#### Sample gdbinit file

```sh
set sysroot /home/developer/buildroot-2017.02.4/output/staging
set solib-search-path /home/developer/buildroot/buildroot-2017.02.4/output/staging/usr/lib

```


### Note on using set sysroot

The default value for the set sysroot variable depends on your toolchain. If your GDB binary was compiled with the --sysroot argument, you won't need to run the set sysroot command - the sysroot will be automatically set to the location specified during compilation. Otherwise the default value will be "" and you might need to set it manually if you are debugging remote processes.

### How to check if Symbols are present in binary

```sh
readelf --debug-dump=decodedline $1
```

# Assembly Language Tool Online

- [Online Tool](https://godbolt.org/)


