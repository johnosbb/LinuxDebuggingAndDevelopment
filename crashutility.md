
# Building the Crash Utility


Install the dependecies

```sh
sudo apt install gcc-multilib g++-multilib lib32z1-dev lib32ncurses5-dev texinfo flex bison
```

Download the crash utility

```sh
git clone https://github.com/crash-utility/crash.git
cd ./crash-utility
make target=ARM #to build for our target
```


We can load a vmcore with

```sh
sudo chown snuc:snuc /home/snuc/debugging-labs/nfsroot/root/vmcore
./crash /home/<user>/debugging-labs/nfsroot/root/vmlinux /home/<user>/debugging-labs/nfsroot/root/vmcore
```
