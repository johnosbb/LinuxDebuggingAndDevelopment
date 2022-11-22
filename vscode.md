# Remote Debugging with VSCode

## VSCode
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
