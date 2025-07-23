## Prepare the build system (MSYS2)

Download the MSYS2 64bit installer from here: https://msys2.github.io/

Follow the instructions of that page otherwise install it using all the defaults (in `c:\msys64`)<br>
Open the MSYS2 MINGW64 shell and install the packages required to build QLC+, by typing:<br>

```shell
pacman --sync --sysupgrade
pacman --sync \
  make \
  automake \
  autoconf \
  libtool \
  mingw64/mingw-w64-x86_64-gcc \
  mingw64/mingw-w64-x86_64-gcc-libs \
  mingw64/mingw-w64-x86_64-cmake \
  mingw64/mingw-w64-x86_64-ninja \
  mingw-w64-x86_64-tools-git
pacman --sync \
  mingw64/mingw-w64-x86_64-libmad \
  mingw64/mingw-w64-x86_64-libsndfile \
  mingw64/mingw-w64-x86_64-flac \
  mingw64/mingw-w64-x86_64-fftw \
  mingw64/mingw-w64-x86_64-libusb \
  mingw64/mingw-w64-x86_64-python-lxml \
  mingw64/mingw-w64-x86_64-nsis
```

## Install the Qt libraries

The latest Qt version can be downloaded via online installers here: https://download.qt.io/official_releases/online_installers/<br>
Make sure to select the latest Qt version for MinGW 64bit and the following additional libraries:
* Qt Multimedia
* Qt Serial Port
* Qt Websockets
* Qt 3D (to build v5)

## Acquire the QLC+ sources

These instructions suppose you have a folder called "projects" in `C:`. If it doesn't exist, create it and 'cd' into it.<br>

If you wish to get the latest released QLC+ version download the source tarball from here:<br>
[https://github.com/mcallegari/qlcplus/releases/latest/](https://github.com/mcallegari/qlcplus/releases/latest/)

If you wish to get the very latest bleeding edge (but only if your intention is to do development or are just curious), download the [GitHub client](https://windows.github.com/) or use the command line:

```shell
git clone https://github.com/mcallegari/qlcplus.git
```

This will create a directory called qlcplus which will contain the latest sources from GIT repository. After you have made the initial clone and later wish to keep living on the bleeding egde, you can just update the sources (instead of making a new clone every time):

```shell
cd /c/projects/qlcplus
git pull
```

### Debug or release mode

If you are a developer and want to contribute to QLC+, the default settings will build a debug version of the program. Please note that a debug version is bigger than a release one and might have worse performances.
If what you need is a production build, then you need to edit the main `CMakeLists.txt` file and change the line starting with

```cmake
set(CMAKE_BUILD_TYPE "Release" ... )
```

Switch between `Debug` and `Release` depending on your needs.

## DMX USB Support

To compile the DMX USB plugin, you need to:

Download the latest SDK from [FTDI Driver page](http://www.ftdichip.com/Drivers/D2XX.htm).<br>
Extract the package contents for example to C:\projects\D2XXSDK<br>
Edit `plugins/dmxusb/src/CMakeLists.txt` to point to the directory you picked:

```cmake
FTD2XXDIR = C:/projects/D2XXSDK
```

Since the SDK provided by ftdichip.com is not compatible with the MSYS2 system, it is necessary to manually create a compatible libftd2xx.a file that will be used at build time:

```shell
cd /c/projects/D2XXSDK/amd64
gendef.exe - ftd2xx64.dll > ftd2xx.def
dlltool -k --input-def ftd2xx.def --dllname ftd2xx64.dll --output-lib libftd2xx.a
```

If you don't need the DMX USB plugin and would like to disable building it completely, edit `<QLC>/plugins/CMakeLists.txt` and put a hash (#) on the line that includes the plugin, like this:

```cmake
# add_subdirectory(dmxusb)
```

## Build QLC+

Now to compile QLC+ you first need to export a variable named `QTDIR` to link to the official Qt libraries:<br>
`export QTDIR=/c/projects/Qt/6.9.0/mingw_64` (adapt the path to where your Qt libraries are installed)<br>

Then build QLC+ like this:

```shell
cd /c/projects/qlcplus
mkdir build && cd build
cmake ..
ninja
ninja install
```

It will install QLC+ and all the basic required DLLs in `C:\qlcplus`.
To complete the dependencies installation do this:

```shell
cd /c/qlcplus
$QTDIR/bin/windeployqt qlcplusengine.dll qlcplusui.dll qlcpluswebaccess.dll Plugins/dmxusb.dll qlcplus.exe
```

If you're quilding QLC+ 5.x do this:

```shell
$QTDIR/bin/windeployqt --qmldir $ROOT_DIR/qmlui/qml qlcplusengine.dll Plugins/dmxusb.dll qlcplus-qml.exe
```