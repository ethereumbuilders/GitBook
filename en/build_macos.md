# Building on MacOS

# Installing cpp-ethereum on OS X

### Using Homebrew

First tap the ethereum brew:
```
brew tap ethereum/ethereum
```

For the latest stable version:
```
brew install ethereum
brew linkapps
```

For the latest cutting edge developer version:
```
brew reinstall ethereum --devel
brew linkapps
```

Then open `AlethZero.app` from your Applications folder. You can also run `eth` (CLI) or `neth` (ncurses interface).

For options and patches, see: https://github.com/ethereum/homebrew-ethereum

### Downloading .app package directly

If you're just interested in the GUI client AlethZero without the command line interfaces, download the latest [stable](http://build.ethdev.com/builds/OSX%20C%2B%2B%20master%20branch/AlethZero-OSX-latest.tar.bz2) or [cutting edge](http://build.ethdev.com/builds/OSX%20C%2B%2B%20develop%20branch/AlethZero-OSX-latest.tar.bz2)  version.