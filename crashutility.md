
# Building the Crash Utility


Install the dependecies

```sh
sudo apt install gcc-multilib g++-multilib lib32z1-dev lib32ncurses5-dev texinfo
```

Download the crash utility

```sh
git clone https://github.com/crash-utility/crash.git
cd ./crash-utility
make target=ARM #to build for our target
```

