# Remote Debugging with GDB Command Line


On the target side we run:

```sh
# gdbserver localhost:2000 /usr/bin/linked_list
Process /usr/bin/linked_list created; pid = 252
Listening on port 2000
Remote debugging from host 192.168.0.1, port 59106

```

On the host side we can run:

```sh
gdb-multiarch -tui -x gdbinit_local ./linked_list
```

Then from gdb
![image](https://user-images.githubusercontent.com/12407183/203612203-b3961300-5d4a-4f40-b266-255ab572181f.png)


```sh
target extended-remote 192.168.0.100:2000
```

