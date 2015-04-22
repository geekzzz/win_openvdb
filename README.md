
Windows Build of OpenVDB 
================================================================================
by lithozine (April 22, 2015)

These instructions, and the repository forks named win_openexr and win_openvdb, 
contain updated files and bug fixes to allow for building of OpenVDB on Windows.

*NOTE*
This build tested on Win7 64-bit, VS 2010, in 64-bit Debug and Release modes.
I will provide support for this platform config only. If you have issues with other compilers,
other versions of windows, or 32-bit builds, please use the openvdb forums.

Post questions/comments to:
  www.openvdb.org/forum, on the thread "[Solution] OpenVDB 3.0 for Win 7 64-bit / VS2010"

The main idea behind this build is to keep a clear separation between 
the source code and the final build outputs and projects. This makes it easy to 
wipe the current build output, while keeping the source code handy to regenerate.
You can be certain that \source will match the github repos, with nothing extraneous.

Instructions follow:

1) Create the following folder structure
----------------------------------------
```
\codes
  \build
    \boost_1_57_0	  Install from boost_1_57_0-msvc-10.0-64.exe at
                    http://sourceforge.net/projects/boost/files/boost-binaries/1.57.0/
    \glew-1.11.0    Install from http://sourceforge.net/projects/glew/files/glew/1.11.0/
    \glfw           Install from http://www.glfw.org/	
    \tbb43          Install from https://www.threadingbuildingblocks.org/
  \source
    \zlib           Git Clone from https://github.com/rchoetzlein/zlib.git
    \win_openvdb    Git Clone from https://github.com/rchoetzlein/win_openvdb.git
    \win_openexr    Git Clone from https://github.com/rchoetzlein/win_openexr.git
```
Notice that the git clone source repositories are kept separate from the build pre-requisites.
At this point, the \build folder should contain unpacked installations of the existing libraries shown above.
The \source folder should contain a git clone of these forked repositories.

*NOTE* Only the base path \codes can be renamed or relocated. All the paths inside, includig \build and \source, and their sub-folders must follow the above structure. The names \build and \source must not be changed.

2) Install and start CMake-gui, version 2.8.12 or later
----------------------------------------

3) ZLib - CMake and Build
----------------------------------------
3.1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\zlib<br>
Build binaries: \codes\build\zlib<br>
3.2. Click Configure, and choose "Visual Studio 10 Win64". <br>
3.3. Now click Generate.<br>
You should see this output:<br>
```
Packaging for /include: D:/Codes/build/zlib/$(Configuration)/zlib.dll -> D:/Codes/build/zlib/lib
Packaging for /include: D:/Codes/build/zlib/$(Configuration)/zlib.lib -> D:/Codes/build/zlib/lib
Packaging for /include: D:/Codes/build/zlib/$(Configuration)/zlibstatic.lib -> D:/Codes/build/zlib/lib
Packaging for /include: D:/Codes/source/zlib/*.h -> D:/Codes/build/zlib/include
Packaging for /include: D:/Codes/build/zlib/zconf.h -> D:/Codes/build/zlib/include
Configuring done
Generating done
```
3.4. Go to the path \code\build\zlib, and open zlib.sln in VS2010
3.5. Select "Release" mode, 64-bit, and Build all.. Should have 6 succeeded, 0 failed.

4) IlmBase - CMake and Build
-------------------------------------
4.1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\win_openexr\IlmBase<br>
Build binaries: \codes\build\IlmBase<br>
4.2. Click Configure, and choose "Visual Studio 10 Win64". <br>
4.3. Now click Generate. <br>
You should see this output:<br>
```
Packaging for /include: D:/Codes/build/IlmBase/Half/$(Configuration)/Half.dll -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/build/IlmBase/Half/$(Configuration)/Half.lib -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/source/win_openexr/IlmBase/Half/*.h -> D:/Codes/build/IlmBase/include
Packaging for /include: D:/Codes/build/IlmBase/Iex/$(Configuration)/Iex.dll -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/build/IlmBase/Iex/$(Configuration)/Iex.lib -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/source/win_openexr/IlmBase/Iex/*.h -> D:/Codes/build/IlmBase/include
Packaging for /include: D:/Codes/build/IlmBase/IexMath/$(Configuration)/IexMath.dll -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/build/IlmBase/IexMath/$(Configuration)/IexMath.lib -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/source/win_openexr/IlmBase/IexMath/*.h -> D:/Codes/build/IlmBase/include
Packaging for /include: D:/Codes/build/IlmBase/Imath/$(Configuration)/Imath.dll -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/build/IlmBase/Imath/$(Configuration)/Imath.lib -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/source/win_openexr/IlmBase/Imath/*.h -> D:/Codes/build/IlmBase/include
Packaging for /include: D:/Codes/build/IlmBase/IlmThread/$(Configuration)/IlmThread.dll -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/build/IlmBase/IlmThread/$(Configuration)/IlmThread.lib -> D:/Codes/build/IlmBase/lib
Packaging for /include: D:/Codes/source/win_openexr/IlmBase/IlmThread/*.h -> D:/Codes/build/IlmBase/include
Configuring done
Generating done
```
4.4. Go to the path \code\build\IlmBase, and open IlmBase.sln in VS2010
4.5. Select "Release" mode, 64-bit, and Build all.. Should have 12 succeeded, 0 failed.

5) OpenEXR - CMake and Build
-------------------------------------
5.1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\win_openexr\OpenEXR<br>
Build binaries: \codes\build\OpenEXR<br>
5.2. Click Configure, and choose "Visual Studio 10 Win64". <br>
5.3. Now click Generate. <br>
You should see this output:<br>
```
Zlib Library: Found at D:/Codes/build/zlib/lib/zlib.lib
IlmBase Library: Found at D:/Codes/build/IlmBase/lib/Half.lib;D:/Codes/openvdb/build/IlmBase/lib/Iex.lib;D:/Codes/build/IlmBase/lib/Imath.lib;D:/Codes/IlmBase/lib/IlmThread.lib
ILMBASE_PACKAGE_PREFIX = D:/Codes/build/IlmBase
Packaging for /include: D:/Codes/source/win_openexr/OpenEXR/IlmImf/*.h -> D:/Codes/build/OpenEXR/include
Configuring done
Generating done
```
5.4. Go to the path \code\build\OpenEXR, and open OpenEXR.sln in VS2010
5.5. Select "Release" mode, 64-bit, and Build all.. Should have 15 succeeded, 0 failed.




WINDOWS - BUILD SYSTEM CHANGES
================================================================================
The following are deeper technical notes on how the build system was
setup for Windows - changes made the source code, new CMake modules, and
new steps for packaging.
* You don't need to know this to build OpenVDB for Win. This is for future reference,
  and hopefully dev integration into the primary branch.







================================================================================
                          OpenVDB Development Repository
================================================================================

This GitHub repository hosts the trunk of the OpenVDB development. This implies
that it is the newest public version with the latest features and bug fixes.
However, it also means that it has not undergone a lot of testing and is
generally less stable than  the production releases that we will continue to
deliver as tar-balls at www.openvdb.org/download

For documentation of the library and code examples see:
www.openvdb.org/documentation/doxygen

General discussion forum:
http://www.openvdb.org/forum


================================================================================
                           Contributor License Agreement
================================================================================

Developers who wish to contribute code to be considered for inclusion in the
OpenVDB distribution must first complete this Contributor License Agreement:
http://www.openvdb.org/download/OpenVDBContributorLicenseAgreement.pdf

and submit it to DreamWorks (directions are in the CLA). We prefer code
submissions in the form of pull requests to this repository, and all code
should adhere to the OpenVDB coding standards:
http://www.openvdb.org/documentation/doxygen/codingStyle.html
