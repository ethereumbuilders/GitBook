# Go

Install all build dependencies.

* [Golang](http://golang.org/dl/) 1.2 or higher (32-bit required)
* Install [Git](http://git-scm.com/) and [Mercurial](http://mercurial.selenic.com/)
* [MinGW32](http://www.mingw.org/) (add X:\MinGW\bin directory to your  PATH)
* Use mingw32-get to install *gmp* packages
* Install [Qt5 for Windows 32-bit MinGW](http://qt-project.org/downloads) (5.2.1 at the moment of writing)
* Install [pkg-config](http://www.freedesktop.org/wiki/Software/pkg-config/) somewhere in your PATH. (read the [instructions](http://stackoverflow.com/questions/1710922/how-to-install-pkg-config-in-windows) here)
* Install [NSIS](http://nsis.sourceforge.net/)

After all these things have been satisfied ```go get -u github.com/ethereum/go-ethereum/cmd/mist```

Once the compilation is completed you can create a setup binary.
- Edit build.bat and change qtPath and mingwPath to the paths of your installed versions.
- right-click the nsi file and select "Compile NSIS Script".

If everything went well you should now have a windows-setup file.