# Go

# Option 1: Install from PPA

For the latest development snapshot, both ppa:ethereum/ethereum and ppa:ethereum/ethereum-dev are needed. If you want the stable version from the last PoC release, simply omit the -dev one.

```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ethereum/ethereum
sudo add-apt-repository ppa:ethereum/ethereum-dev
sudo apt-get update
sudo apt-get install go-ethereum
```

Run `mist` for the GUI or `ethereum` for the CLI.

# Option 2: Automatic installation

[This Mist install script](https://gist.github.com/tgerring/41275735ebb057b58ea8) will install everything required from a fresh Ubuntu 14.04 installation and start running Mist.

```
wget https://gist.githubusercontent.com/tgerring/41275735ebb057b58ea8/raw/e166190f242d873cc8edeae5cb8f1cc667a126ac/goethereumqt54.sh -O install
chmod +x install 
./install
```

# Option 3: Manual build from source

## Installing Go

Verify that Go is installed by running `go version` and checking the version. If not, see [Installing Go](https://github.com/ethereum/go-ethereum/wiki/Installing-Go)

## Prerequisites

Mist depends on the following external libraries to be installed:
* [GMP](https://gmplib.org)
* [Readline](http://www.gnu.org/s/readline/)
* [Qt 5.4](http://www.qt.io/download-open-source/).

First install GMP & Readline from repositories:
```
sudo apt-get install -y libgmp3-dev libreadline6-dev
```

Second, follow the instructions for [Building Qt](https://github.com/ethereum/go-ethereum/wiki/Building-Qt)

## Installing Mist
At last you will now be finished with all the prerequisites. The following commands will build the Ethereum Mist GUI client for you:

    go get -u github.com/ethereum/go-ethereum/cmd/mist

**Note: Mist does not automatically look in the right location for its GUI assets. For this reason you have to launch it from its build directory:**

    cd $GOPATH/src/github.com/ethereum/go-ethereum/cmd/mist && mist

To eliminate the need to remember this cumbersome command, you can create the following a file in $GOPATH/bin :

    #!/bin/bash
    cd $GOPATH/src/github.com/ethereum/go-ethereum/cmd/mist && mist

Name the file 'misted' and make it executable:

    chmod +x $GOPATH/bin/misted

Now mist can be run with the command `misted`