# C++

# Installing cpp-ethereum on Ubuntu 14.04, 64 bit

For the latest stable version:
```
sudo add-apt-repository ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```

If you want to use the cutting edge developer version:
```
sudo add-apt-repository ppa:ethereum/ethereum
sudo add-apt-repository ppa:ethereum/ethereum-dev
sudo apt-get update
sudo apt-get install ethereum
```

(Note: Installing this still pulls in some unnecessary packages. See https://github.com/ethereum/ethereum-buildbot/issues/27 for status on this issue.)

Run `alethzero` for the GUI, `eth` for the CLI or `neth` for the ncurses interface.

## Installing the Mix IDE

Mix, the devekoper IDE is still in its infancy and still needs a lot of work. If you are adventurous, you can try to run MIX by installing the cutting edge developer version of cpp-ethereum (see above) and then add this:
```
sudo add-apt-repository ppa:beineri/opt-qt54-trusty
sudo apt-get update
sudo apt-get install qt54-meta-full
source /opt/qt54/bin/qt54-env.sh
mix
```

## Installing an ethereum node server
To run a node server on Ubuntu, follow [these instructions](https://github.com/ethereum/cpp-ethereum/wiki/Building-on-Ubuntu#building-and-installing-a-cpp-ethereum-node-server-on-ubuntu-1404-64-bit).
