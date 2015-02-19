# C++

### With CMake and Visual Studio Express 12.0 (VS2013)

### Initial configuration

Ensure you have installed:
- [CMake 3.0+](http://www.cmake.org/download/) - The cross-platform, open-source make system.
- [Visual Studio Express 2013 for Windows Desktop](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-desktop) - VS2013 for Windows Desktop is a minimum as cpp-ethereum uses languages features not supported in 2012. The Professional edition also works. (tested 10/2014)
- [Windows 7 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=8279) or [Windows 8 SDK](http://msdn.microsoft.com/en-us/windows/bg162891.aspx) - for files like winsock2.h and gdi32.lib
- [Git for Windows 1.9.0+](http://msysgit.github.io/) - This for retrieving source from Github.
- [7-Zip](http://www.7-zip.org/download.html) - Ensure that 7z.exe has been added to the PATH.

### Get the source
Execute the following step to download the latest source code
```
C:\> git clone https://github.com/ethereum/cpp-ethereum.git
```

### Retrieve dependecies
Execute the following commands within a command shell to download all dependencies:

#### 1. Generate solution with CMake
```
cd extdep
C:\cpp-ethereum\extdep> cmake . -G "Visual Studio 12"
```
#### 2. MSBuild
```
C:\cpp-ethereum\extdep> msbuild project.sln /p:Configuration=Release
```
Note: The 'Debug'-configuration is also supported.

### Build Ethereum project
Execute the following commands within a command shell to build the project:

#### 3. Generate solution with CMake
First generate the ethereum solution and visual studio project files
```
cd ..
mkdir build
cd build
C:\cpp-ethereum\build> cmake .. -G "Visual Studio 12"
```
Note: In-source build (running cmake in root directory) is not currently supported.
#### 4. Compile with MSBuild
```
C:\cpp-ethereum> msbuild ethereum.sln /p:Configuration=Release
```
Note: The 'Debug'-configuration is also supported.

And it should now be possible to compile and debug by opening the generated Ethereum solution with Visual Studio 2013.

### Fresh builds
After setting up the dependencies and running your first successful build, unless there are changes to any of the dependencies, you can make a fresh build from the latest code much faster by just fetching the new code and rebuilding. 

1. In the directory `C:\cpp-ethereum>`, run the command `git pull`.
2. Re-run steps 3 & 4.

#### Troubleshooting
