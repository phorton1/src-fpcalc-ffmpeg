phorton1/ffmpeg
=========================

## Contents

This repository contains the source code, scripts, and makefiles to build specific **stripped** versions of **ffmpeg** *archive libraries* suitable for linking fpCalc executables and Android shared libraries as desribed in the bitbucket phorton1/chromaprint repository.  This project generally **works with** that repository to build specific platforms and versions of fpCalc executables and Android shared libraries.  It is **not** intended to build ffmpeg libraries for general use, though it could *easily be modified* to do so.


This repository is capable of building (at least) the following four specific **versions** of ffmpeg:

- 0.9 - the official release version of ffmpeg that I believe to be closest in functionality to that which was used to create the November 23, 2013 "official acoustid" windows build of fpcalc.exe available [here](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.1-win-i686.zip), and which is used in musicBrainz windows release of [picard](http://picard.musicbrainz.org/)
- 0.11 - a release slightly "later" than the 0.9 release
- 2.7 - the most current offical release of ffmpeg as of this writing
- tip - the tip of the ffmpeg repository as of it's fork to this site on or about July 23, 2015

for the following **target platforms**:

- Win32
- Linux
- Android x86
- Android armeabi
- Android armeabi-v7a

The build mechanism presented in this repository has been tested on Linux (x86 Ubuntu 12.04) and Windows 8 **hosts**. That is to say, that the above versions of ffmpeg can generally be *built* on either Linux or Windows, the exception being that the Linux ffmpeg *archive libraries* can *only* be built on a Linux host ... they are not built on a Windows machine with this system.

This build system *should* work on any platform that presents a bash shell and which provides a gcc-like developement environment. It should be fairly straightfoward to modify it to inncorporate additional specific platforms (i.e. mips, OSX, etc).

In general this (these) project(s) utilize **out of tree** builds, so that you can maintain builds for  *ALL* of the above configurations simultaneously if you desire.  In fact, it is possible to set up a machine to host both Windows (cgywin) and Ubuntu (vbox) development environments, and to **share this repository** between them, so that there is a single source tree, and single result tree, and yet you can maintain builds for **ALL** of the above versions on **both** Linux and Windows hosts simultaneosly.


## Configuring and Building

### 1. Configuration Requirements

#### GIT

This build system requires the use of **git**.  It *could* be run without git, using specific bitbucket commands to download specific versions of the repository, but that is beyond the scope of this document to describe.  **Git** is used to retrieve the source from the internet to your build machine, and to select the specific version to build, and so is considered a requirement of this build mechanism.  If you don't have GIT, you will need to install it.

#### cgyWin for Windows machines

Building on Windows requires that you install the [minGW](http://www.mingw.org/) development environment, including **gcc** and **g++**, and that you know how to run its **mSys bash shell**.  If you will be building the Win executable, you should also install **yasm** by copying the yasm.exe file from the zip [here](http://www.tortall.net/projects/yasm/releases/yasm-1.3.0-win32.exe) into the **mingw/bin** directory.

#### Android NDK

If you are going to build Android executables or shared libraries, you will need to download and install the [Android NDK](https://developer.android.com/tools/sdk/ndk/index.html).  The version at the time of this writing is **r10e**.   You must also set an **environment** variable **NDK** to point at the root of the ndk directory.  In Windows this is done in My Computer - Advanced Settings Environment Variables.  In Linux this is done in a variety of ways, including editing the .profile file in your $HOME directory.

```bash
    export NDK=/blah/android-ndk-r10e
```

#### MinGW32 Cross Compilation Environment on Linux

If you will be building the Windows exectuble on a Linux machine, you will need to install the MinGw32 Cross Compilation Environment as per the instructions at the top of this [page](https://github.com/lalinsky/chromaprint/wiki/Building-on-Windows).

```bash
    $ sudo apt-get install mingw32
```

### 2. Get the Source

You will be creating a directory structure containing at least **ffmpeg** and **chromaprint** source directories, as well as *_build* and *_install* subdirectories for the *out of tree* builds and results.  The name and/or location of that directory are unimportant to the build system.  For our example, we will use /blah/fpcalc as the *master* directory for this (set of) project(s).  So to begin with

**Create the *master* source directory** and change to it ...

```bash
    mkdir /blah/fpcalc
    cd /blah/fpcalc
```

Use **git clone** to download a *full copy* of this repository and **check-out** the specific version you will be building:

```bash
    git clone https://phorton1@bitbucket.org/phorton1/ffmpeg.git
    cd /blah/fpcalc/ffmpeg
    git checkout multi-0.9
```

This will create the directory **/blah/fpcalc/ffmpeg**. In our example we will start off by building the 0.9 version of ffmpeg.

### 3. Configure and Build

*All following examples assume that you are running a bash shell, either "real" Linux or cgyWin*

Within a given version (checkout) you can configure and build one or more platforms using the **multi-configure** and **multi-make** shell scripts.

### **multi-configure**
takes *multiple parameters* from the list **all**, **win**, **x86**, **arm**, **arm7**, and **host**:

- all = build all platforms
- win = build for windows
- x86 = build for android-x86 platform
- arm = build for android-armeabi
- arm7 = build for android-armeabi-v7a
- host = (linux only) build for the host (linux)

The **host** build is used on linux to build the linux version of the ffmpeg libraries.  It is not available on Windows hosts, where you use **win** to build ffmpeg for Windows

#### multi-make

takes **one** of those parameters, along with additional optional "make" parameters like **clean**, **noisy**, and **install**.

#### example: configure and build for armeabi-v7a

Assuming you are building on a Linux machine, if you

```bash
    cd /blah/fpcalc/ffmpeg
    ./multi-configure arm
    ./multi-make arm install
```

This will build and "install" (make them available for chromaprint) the various ffmpeg libraries in the /blah/fpcalc/**_build** directory.  After this command has succesfully run, in our **multi-0.9** example, you would see the following directory structure under /blah/fpcalc/_build:

    +-- blah
        +-- fpcalc
            +-- _build
                +-- ffmpeg
                    +-- 0.9                 = the version being built
                        +-- _linux          = the host platform it was built on
                            +-- arm         = the target platform it was built for
                                +-- bin
                                +-- build   = out of tree object files, etc
                                +-- include = h files to be used by chromaprint
                                +-- lib     = .a archive libraries to be used by chromaprint

Had you been building on a Windows machine, the host platform would be **_win** instead of **_linux**, and had you built for more than one platform, there would be additional different target directories (x86, win, etc) underneath it.

Assuming you are building phorton1/chromaprint next, this directory structure is not important for you to know, but it is used by, and known by that repository's build mechanism, and is presented here for completeness.

#### example: configure and build for Windows and Linux (host)

```bash
    cd /blah/fpcalc/ffmpeg
    ./multi-configure win host
    ./multi-make win install
    ./multi-make host install
```

In a Linux build environment, this will build the libraries needed by phorton1/chromaprint for building the Windows fpcalc.exe and Linux (host) fpcalc executables.


#### example: configure and build for all target platforms

*These are the commands that I personally use most when building with this repository*

Assuming you have met all the requirements, installed the NDK and set its environment variable, and so forth, you can build "all" targets with the following commands on Windows:

```bash
    cd /blah/fpcalc/ffmpeg
    ./multi-configure all
    ./multi-make all
```

or the following comamnds in Linux:

```bash
    cd /blah/fpcalc/ffmpeg
    ./multi-configure all host
    ./multi-make all
```

The difference is that **host** is not automatically included in multi-configure "all".  You must explicitly specify "host" in the multi-configure step (if you want to build the Linux (host) exectuable on Linux). Once it has been configured, however, it IS automatically built by multi-make all.  And remember that you **cannot specify "host" on a Windows machine** (you use "win" to build the windows version of ffmpeg).

The separation of the "host" build currently means "build the linux stuff" on a linux platform. Hopefully it will mean "build the OSX stuff" on an OSX platform at some point in time.


### 4. Building Multiple Versions

To build multiple **versions** you loop through the checkout-configure-build process.  For example, to build the windows ffmpeg libraries for the 0.9, 2.7, and "tip" versions of this repository, you would execute the following commands:

```bash
    cd /blah/fpcalc/ffmpeg

    git checkout multi-0.9
    ./multi-configure win
    ./multi-make win

    git checkout multi-2.7
    ./multi-configure win
    ./multi-make win

    git checkout multi-tip
    ./multi-configure win
    ./multi-make win
```

## Change Details

This project generally uses the same ffmpeg flags as specfied at Lukas Lalinsky's [page](https://oxygene.sk/2011/04/minimal-audio-only-ffmpeg-build-with-mingw32/) for creating a **stripped** version of ffmpeg.

There are *slight modifications* needed to those flags, and the ffmpeg **configure** script between different versions of ffmpeg, so this project separates those differences into easy to manage (and checkout) different branches of the repository.  When you check out multi-0.11 you get the proper configure script, along with the correct version of multi-configure to work with that version of ffmpeg.

There were also *cosmetic modifications* to the ffmpeg **common.mak** script to prettify the build process, including showing which platform is being built, and supresssing warnings.

Otherwise, the **ffmpeg source code was untouched**.

## License and Credits

Credits to **Michael Neidermeyr** and the many people that have worked long and hard on the ffmpeg.org project.

This project in no way alters the FFmpeg licensing, whose codebase is mainly LGPL-licensed with optional components licensed under GPL. Please refer to the LICENSE file for detailed information.
