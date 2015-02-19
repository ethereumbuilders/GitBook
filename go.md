# Go

Assumptions:
* Go 1.2+ is installed. If not refer to [this](https://github.com/ethereum/go-ethereum/wiki/Installing-Go) page.
* Brew is installed. If not, run `ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"` and follow the prompts

Install Qt 5.4.x:

```brew install qt5```

Now configure some path for go-qml to properly build:

```
export PKG_CONFIG_PATH=/usr/local/Cellar/qt5/5.4.0/lib/pkgconfig
export CGO_CPPFLAGS="-I/usr/local/Cellar/qt5/5.4.0/include/QtCore/5.4.0/QtCore"
export LD_LIBRARY_PATH=/usr/local/Cellar/qt5/5.4.0/lib
```

Compile **Mist**

```
go get -u github.com/ethereum/go-ethereum/cmd/mist
```

Run Mist (_This is necessary as a separate step due to an outstanding bug requiring mist to be run from its build directory_)

```
cd $GOPATH/src/github.com/ethereum/go-ethereum/cmd/mist && go build && ./mist
```

This should start Mist and connect you to the Ethereum network. However, if you want a traditional `*.app` file we'll need to use our [build tool](https://github.com/ethereum/go-build).

```
git clone git@github.com:ethereum/go-build.git
cd go-build/osx && python build.py
```

This will save a `Mist.app` in the **osx** directory.

## Option 3: Building from source easy guide

**WARNING: Guide is outdated but remains for references**

We now have a simple tutorial to install from source:
http://forum.ethereum.org/discussion/905/go-ethereum-cli-ethereal-simple-build-guide-for-osx