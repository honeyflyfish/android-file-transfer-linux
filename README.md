# Android File Transfer For Linux

[![License](http://img.shields.io/:license-GPLv2-blue.svg)](https://github.com/whoozle/android-file-transfer-linux/blob/master/LICENSE)
[![Version](http://img.shields.io/:version-2.2-green.svg)](https://github.com/whoozle/android-file-transfer-linux/blob/master)
[![Build Status](https://travis-ci.org/whoozle/android-file-transfer-linux.svg?branch=master)](https://travis-ci.org/whoozle/android-file-transfer-linux)

Android File Transfer for Linux — reliable MTP client with minimalistic UI similar to [Android File Transfer for Mac](https://www.android.com/intl/en_us/filetransfer/).

It just works™.

## Do I need it?

If you're happy with `gmtp`/`gvfs`/`mtpfs` or any other mtp software, you might not need this software (but give it a try!).

If you're suffering from crashes, missing tags and album covers, usb freezes and corrupted files, this software is right for you.

## Features

* Simple Qt UI with progress dialogs.
* FUSE wrapper (If you'd prefer mounting your device), supporting partial read/writes, allowing instant access to your files.
* No file size limits.
* Automatically renames album cover to make it visible from media player.
* No extra dependencies (e.g. `libptp`/`libmtp`).
* Available as static/shared library.
* Simple CLI tool.

## FAQ
##### You're getting «ioctl(_fd, USBDEVFS_CLAIMINTERFACE, &interfaceNumber): Device or resource busy» or «Device is already used by another process» exception/message box right after you started the application. This clearly indicates that some other process is accessing MTP device right now.
You could do the following steps to find it:
* Open you console emulator (gnome-terminal, konsole, whatever) and type: ```lsusb``` (sudo apt-get install usbutils if it did not start) and find your device in its output, for example
```
Bus 006 Device 070: ID 18d1:4ee2 Google Inc. Nexus 4 (debug)
```
* Start fuser ```sudo fuser /dev/bus/usb/<BUS>/<DEVICE>``` (sudo apt-get install psmisc if it did not start)
* It should output something like this: ```/dev/bus/usb/006/070: 23253 24377``` (actually, there could be more of them after semicolon, like : 24377, 24378, …) so, 23253 and 24377 are the pids of the processes which opened your device.
* So, finally run:
```
ps -x -q 23253
23253 ?        Sl     0:00 /usr/local/bin/android-file-transfer
ps -x -q 24377
24377 ?        Sl    21:14 adb -P 5037 fork-server server
```
Usually, adb is not offending process, because it uses another interface, so the /usr/local/bin/android-file-transfer is the one


## Building instructions

### Prerequisites

* You will need qt libraries for building ui program. If you want to use only library (*Qt is not needed*), you could turn the option ```BUILD_QT_UI``` off.
* For ubuntu and other debian-based distros use the following command:

  ```shell
  sudo apt-get install build-essential cmake libqt4-dev ninja-build libfuse-dev
  ```
* Basically, you need `libqtX-dev` for UI, `libfuse-dev` for FUSE interface, `cmake`, `ninja` or `make` for building the project. You could use libqt5-dev as well.

### Building with ninja

```shell
mkdir build
cd build
cmake -G Ninja ..
ninja

./qt/android-file-transfer
```

### Building with make

```shell
mkdir build
cd build
cmake ..
make

./qt/android-file-transfer
```

### Installation

`sudo ninja install` or `sudo make install` will install program into cmake prefix/bin directory (usually /usr/local/bin)


## How to use

### FUSE interface

```shell
mkdir ~/my-device
./aft-mtp-mount ~/my-device
```
Remember, if you want album art to be displayed, it must be named 'albumart.xxx' and placed *first* in the destination folder. Then copy other files.
Also, note that fuse could be 7-8 times slower than ui/cli file transfer.

### QT user interface

1. Start application, choose destination folder and click any button on toolbar.

2. The options available there are: `Upload Album`, `Upload Directory` and `Upload Files`.
   The latter two are self-explanatory. `Upload album` tries searching source directory for album cover and sets best available cover.

3. You could drop any files or folders right into application window, the transfer will start automatically.

### Known problems

* Samsung removed android extensions from MTP, so fuse will be available readonly, sorry. Feel free to post your complaints to http://developer.samsung.com/forum/en
* Sometimes downloading fails with usb timeout, then phone becomes unresponsive. [Android bug #75259](https://code.google.com/p/android/issues/detail?id=75259)
* Objects created in UI will not show up in FUSE filesystem. [Android bug #169547](https://code.google.com/p/android/issues/detail?id=169547)

The actual list of all known problems and bugs available [here](https://github.com/whoozle/android-file-transfer-linux/issues)

## License

Android File Transfer for Linux is released under [GNU GPLv2 License](https://github.com/whoozle/android-file-transfer-linux/blob/master/LICENSE).

Copyright © 2015 Vladimir Menshakov
