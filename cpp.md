# C++


### Prerequisites
* Latest Xcode on Mavericks (at least Xcode 5.1+).
* Homebrew package manager  http://brew.sh *or* MacPorts package manager  http://macports.org
* XQuartz  http://xquartz.macosforge.org/landing/

### Install dependencies

Using homebrew:

    brew install boost --c++11 # this takes a while
    brew install boost-python --c++11
    brew install cmake qt5 cryptopp miniupnpc leveldb gmp
    brew tap ethereum/ethereum
    brew install jsonrpc

Or, using MacPorts:

    port install boost +universal # this takes a while
    port install cmake qt5-mac libcryptopp miniupnpc leveldb gmp

To run Aleth zero or to enable the remote-JavaScript API functionality, you must have the JSON-RPC library installed.

You may encounter issues build issues with jsonrpc-cpp.  If you have homebrew installed, run:

    brew tap ethereum/ethereum
    brew reinstall jsonrpc

Otherwise, please remove jsonrpc-cpp library and follow these steps again:

```
git clone https://github.com/cinemast/libjson-rpc-cpp
cd libjson-rpc-cpp
git reset --hard eaca2481e2889d5a5b748383fb02b1d395969cd4
mkdir -p build
cd build
cmake .. && make
sudo make install   #Not required, but makes it easier to use
cd ../../
```

### Clone source repo and create build folder
    git clone https://github.com/ethereum/cpp-ethereum.git
    cd cpp-ethereum
    mkdir -p build # create build folder ('build' ignored by git)

### Compile 
    cd build
    cmake ..
    make -j6
    make install

This will also install the cli tool and libs into /usr/local.

### XCode Instructions
From the project root:
```
mkdir build_xc
cd build_xc
cmake -G Xcode ..
```
This will generate an xcode project file along with some configs for you: ethereum.xcodeproj . Open this file in XCode and you should be able to build the project

## Troubleshooting

Linking errors can usually be resolved by ensuring correct paths:

    export LIBRARY_PATH=/opt/local/lib # this is MacPorts's default

Make sure your `macdeployqt` can be found. Ethereum expects it to be in `/usr/local/opt/qt5/bin/macdeployqt` so symlink it if it's not:

    ln -s `which macdeployqt` /usr/local/opt/qt5/bin/macdeployqt

If you're not planning to develop, make sure you are building from the master branch, not from develop or any others.  These branches can often be broken or have other requirements.