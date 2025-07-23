# QLC+ Installation from sources on macOS using Qt5

## **Note: at the moment it is possible to build QLC+ only on Intel Macs and only with homebrew.**

## Development environment

You need to download and install two components before you can compile QLC+ from sources on macOS:

**Apple XCode** development tools (just the Mac version will do, no need for iPhone stuff). Some recent versions might need to install Command Line Tools after base packet is installed (XCode->Preferences->Downloads)<br>
**The Qt5 Framework**<br>
Download the latest Qt5 version via online installer: https://download.qt.io/official_releases/online_installers/<br>
Install the framework where you want. In this guide we'll be using this path: `/Users/myuser/Qt5.15.2`<br>

## Dependencies

QLC+ and some plugins require additional external packages including: `libftdi`, `libmad`, `libsndfile` and `fftw3`

These dependencies are easily available through homebrew.

From a Terminal, download and install [Homebrew](https://brew.sh/) like this:

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then install the packages required by QLC+:

```shell
brew install fftw libftdi mad libsndfile ola
```

## Getting the sources from GIT

Open the terminal window (under Applications/Utilities in Finder) and write one of the following commands:

If you wish to get the latest released QLC+ version: [https://github.com/mcallegari/qlcplus/releases/latest/](https://github.com/mcallegari/qlcplus/releases/latest/)<br>

If you wish to get the very latest bleeding edge (but only if your intention is to do development or are just curious):

```shell
git clone https://github.com/mcallegari/qlcplus.git
```

You might be asked to accept an SSL site certificate. After you have accepted it, the sources will be downloaded to a directory called qlc under your home directory (where the terminal usually starts at). It can take a while, depending on your internet connection speed.

### Debug or release mode

If you are a developer and want to contribute to QLC+, the default settings will build a debug version of the program. Please note that a debug version is bigger than a release one and might have worse performances.
If what you need is a production build, then you need to edit the main `CMakeLists.txt` file and change the line starting with

```cmake
set(CMAKE_BUILD_TYPE "Release" ... )
```

Switch between `Debug` and `Release` depending on your needs.

### Plugins build note

QLC+ needs several external dependencies to be compiled with all the plugins support.<br>
If you want to exclude some of them from the build process then just comment them out by placing the character `#` at the beginning of the plugin line in the file `plugins/CMakeLists.txt`
For example:

```cmake
# add_subdirectory(ola)
```

## Compiling

After the sources have been cloned out from the GIT repository, issue these commands to start building QLC+:

```shell
cd qlcplus
mkdir build && cd build
cmake -DCMAKE_PREFIX_PATH="/Users/myuser/qt/5.15.2/clang_64/lib/cmake" ..
make -j4
```

> [!NOTE]
> Replace the Qt path with the one installed in your system.

You should see the terminal window fill up with compiler calls. Go grab a cup of your preferred beverage, as this can take anything from about a minute to several minutes, depending on your system performance. If you see any errors, please report it in the QLC+ forum (development forum).

Finally, `make` says something like "Nothing to be done for 'first'. That's it, you're done!

## Installation

When QLC+ has been successfully compiled, you can install it into your home directory as QLC+.app by issuing the following command to the terminal:

```shell
make install
```

Don't worry; everything is installed inside this one application bundle in your home directory. Go to Finder and navigate to your home directory. You should see the QLC+ logo there and if you dare click it, QLC+ launcher should open. You can move the application bundle manually under Applications if you wish. Have fun! :)

## Package creation

If you wish to create a distributable .dmg package, that doesn't require the presence of Qt SDK, XCode or Homebrew, type the following commands after cloning the QLC+ sources right into your terminal window:

```shell
export QTDIR=/Users/myuser/qt/5.15.2/clang_64
./create-dmg-cmake.sh
```

> [!NOTE]
> Replace the Qt path with the one installed in your system.

This creates an Apple `.dmg` package in the `dmg` folder of the QLC+ sources tree.
This script works also on non-built sources, so you just need to follow the steps above up to `cd qlcplus`, right after `export QTDIR...`