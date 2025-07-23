### QLC+ Installation from sources on Linux (Debian, Ubuntu, Fedora, RedHat)
### Pre-requisities

You need a number of packages installed before you can compile QLC+ from sources. Everything here happens in the terminal window, so launch one now. Usually it's an entry under Accessories in your desktop main menu.

### Ubuntu/Debian/Mint

Issue these commands to install the required packages for an Ubuntu system:

```shell
sudo apt-get update
sudo apt-get install \
  g++ \
  make \
  cmake \
  git \
  build-essential \
  qtchooser \
  qt5-qmake \
  qtbase5-dev \
  qtbase5-dev-tools \
  qtscript5-dev \
  qtmultimedia5-dev \
  libqt5multimedia5-plugins \
  qttools5-dev-tools \
  qtdeclarative5-dev \
  libqt5svg5-dev \
  qttools5-dev \
  libqt5serialport5-dev \
  libqt5websockets5-dev \
  fakeroot \
  debhelper \
  devscripts \
  pkg-config \
  libxml2-utils \
  libglib2.0-dev \
  libpulse-dev \
  libxkbcommon-dev
sudo apt-get install \
  libasound2-dev \
  libusb-1.0-0-dev \
  libftdi1-dev \
  libudev-dev \
  libmad0-dev \
  libsndfile1-dev \
  libfftw3-dev
```

### Fedora/RedHat

Issue these commands to install the required packages for a Fedora/RedHat system:

```shell
su -
yum update
yum install \
  gcc-c++ \
  qtbase5-common-devel \
  qtmultimedia5-devel \
  libftdi-devel \
  libusb-devel \
  alsa-lib-devel \
  rpm-build \
  git \
  libudev-devel \
  libsndfile-devel \
  libmad-devel
yum install \
  systemd-devel \
  fftw-devel \
  qt5-qtscript-devel \
  qt5-qtmultimedia-devel \
  qt5-qtbase-devel # Fedora 21
```

Notice that there's a space between su and - and that you need to give the root user password for su. When you're done with these commands, become a normal user again with:

```shell
exit
```

### QLC+ sources

If you wish to get the latest released QLC+ version:<br>
[https://github.com/mcallegari/qlcplus/releases/latest/](https://github.com/mcallegari/qlcplus/releases/latest/)

If you wish to get the very latest bleeding edge (but only if your intention is to do development or are just curious):

```shell
git clone https://github.com/mcallegari/qlcplus.git
```

This will create a directory called qlcplus which will contain the latest sources from GIT repository. After you have made the initial clone and later wish to keep living on the bleeding egde, you can just update the sources (instead of making a new checkout each time):

```shell
cd qlcplus
git pull
```

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

### Build OLA (Open Lighting Architecture)

This step is optional depending if you need OLA or not. See previous paragraph in case you want to disable the OLA plugin.

To build the sources, acquire the latest tarball from [github](https://github.com/OpenLightingProject/ola/releases/latest)

Extract the package and enter into the OLA folder.<br>
Follow the [Linux build instructions](http://www.openlighting.org/ola/linuxinstall). <br>
Then, when build time comes, type:

```shell
./configure --prefix=/usr
make
sudo make install
```

### Compile

Now you have two choices: either go ahead with the compilation and manual installation or, spend a little more time with packages in order to create separate QLC+ packages that you can easily upgrade (and uninstall) later. If you wish to do everything manually, continue reading. If you wish to create packages for Ubuntu/Debian, skip to the Package Creation section on this page.

If you wish to build QLC+ with the latest Qt version, you can get it via online installers available here: https://download.qt.io/official_releases/online_installers/

**Note:** Out-of-tree builds are not tested and most probably do not work.

Issue the following commands to start building QLC+:

```shell
cd qlcplus
mkdir build && cd build
```

To build with an official Qt package issue the following:

```shell
cmake -DCMAKE_PREFIX_PATH="/home/<user>/Qt/5.15.2/gcc_64/lib/cmake" ..
```

To build with the system Qt package run the following:

```shell
cmake -DCMAKE_PREFIX_PATH="/usr/lib/x86_64-linux-gnu/cmake/Qt5" ..
```

Then finally type

```shell
make
```

To speed up the build process, if your computer has a multicore CPU you can use the -j option followed by the number of cores of your CPU, like this:

```shell
make -j4
```

You should see your terminal filling with compiler messages and a completion percentage for quite a while.
Everything should go smoothly until the end. If it doesn't happen and you see misterious building errors try either to clone the repository again or completely clean your source tree and start over again by deleting the `build` folder and creating it again.

### Install

OK. When the compiler is done, issue this command to install QLC+ to your system:

```shell
sudo make install
```

or, for non-sudo systems like Debian, Fedora etc:

```shell
su -c "make install"
```

Now you're done. Type

```shell
qlcplus
```

to start using QLC+. If you wish to edit/create fixture definitions, type:

```shell
qlcplus-fixtureeditor
```

(At least) on Debian-based systems, the installation script creates desktop menu entries that are usually available in your desktop main menu, under the Other category.

### Debian/Ubuntu Package Creation

From the build folder, issue the following command:

```shell
cpack -G DEB
```

The package is now being built. If the process fails at an early stage, you are probably missing some dependency package. See above what packages you need to install. When the package script is done, the newly-created packages appear to the folder, where you have the qlcplus folder, i.e. you need to go up once:

```shell
cd ..
```

To install the packages, just type:

```shell
su -c "dpkg -i <package name>.deb"
```

### Fedora/RedHat package creation

Go to the newly-created qlc sub-directory and issue the following command:

```shell
./create-rpm.sh
```

The script will create an RPM build directory structure under your home directory (`~/rpmbuild`) and build the packages there. When the packager is done, go and see the newly-created packages:

```shell
cd ~/rpmbuild/RPMS
ls
```

To install the packages, just type:

```shell
su -c "rpm -Uvh <package name>.rpm"
```

To install all packages, you can type:

```shell
su -c "rpm -Uvh qlcplus*.rpm"
```

You can find more information from the InstallationRedhat? pages (after they have been written, that is).