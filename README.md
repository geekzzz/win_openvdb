
Windows Build of OpenVDB 3.0.0
================================================================================
by yuyonghang (June 24, 2016)

The repository forks named win_openexr and win_openvdb, contain updated cmake files, packaging steps, and fixes to allow for building of OpenVDB on Windows.
The following instructions is the modification of rchoetzlein's instructions on  windows build of openvdb,folked from https://github.com/rchoetzlein/win_openvdb

*NOTE*
This build is tested on Win10 64-bit, VS 2015, in 64-bit Release modes.The solution of error during build is also provided.



The main idea behind this build is to keep a clear separation between 
the source code and the final build outputs and projects. This makes it easy to 
wipe the current build output, while keeping the source code handy to regenerate.
The original git content in \source will not be modified during the build.

Instructions follow:

1) Create the following folder structure ,the root directory is called codes folder,and contains two sub-folders including build and source folder    build folder contains binary libs and source folder contains source code to be built
----------------------------------------
Retrieve the packages from the links provided, and use git clone for the \source repos.
```
\codes
  \build
    \boost_1_61_0	  Install from boost_1_61_0-msvc-14.0-64.exe at
                    https://sourceforge.net/projects/boost/files/boost-binaries/1.61.0/
    \glew-1.11.0    Install from http://sourceforge.net/projects/glew/files/glew/1.11.0/
    \glfw           Install from http://www.glfw.org/	
    \tbb44          Install from https://www.threadingbuildingblocks.org/
  \source
    \zlib           Git Clone from https://github.com/rchoetzlein/zlib.git
    \win_openvdb    Git Clone from https://github.com/rchoetzlein/win_openvdb.git
    \win_openexr    Git Clone from https://github.com/rchoetzlein/win_openexr.git
```
Notice that the git clone source repositories are kept separate from the build pre-requisites.
At this point, the \build folder should contain unpacked installations of the existing libraries shown above.
The \source folder should contain a git clone of these forked repositories. Using git clone command.

*NOTE* Only the base path \codes can be renamed or relocated. All the paths inside, includig \build and \source, and their sub-folders must follow the above structure. The names \build and \source must not be changed.

2) Install and start CMake-gui, version 2.8.12 or later   https://cmake.org/download/
----------------------------------------

3) ZLib - CMake and Build
----------------------------------------
3.1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\zlib<br>
Build binaries: \codes\build\zlib<br>
3.2. Click Configure, and choose "Visual Studio 14 2015 Win64". <br>
3.3. Now click Generate.<br>
You should see this output:<br>
```
Packaging for /include: E:/lib/codes-openvdb/build/zlib/$(Configuration)/zlib.dll -> E:/lib/codes-openvdb/build/zlib/lib
Packaging for /include: E:/lib/codes-openvdb/build/zlib/$(Configuration)/zlib.lib -> E:/lib/codes-openvdb/build/zlib/lib
Packaging for /include: E:/lib/codes-openvdb/build/zlib/$(Configuration)/zlibstatic.lib -> E:/lib/codes-openvdb/build/zlib/lib
Packaging for /include: E:/lib/codes-openvdb/source/zlib/*.h -> E:/lib/codes-openvdb/build/zlib/include
Packaging for /include: E:/lib/codes-openvdb/build/zlib/zconf.h -> E:/lib/codes-openvdb/build/zlib/include
Configuring done
Generating done
```
3.4. Go to the path \code\build\zlib, and open zlib.sln in VS2015<br>
3.5. Select "Release" mode, x64, and Build all.. Should have 6 succeeded, 0 failed.<br>

4) IlmBase - CMake and Build
-------------------------------------
4.1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\win_openexr\IlmBase<br>
Build binaries: \codes\build\IlmBase<br>
4.2. Click Configure, and choose "Visual Studio 14 2015 Win64". <br>
4.3. Now click Generate. <br>
You should see this output:<br>
```
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/Half/$(Configuration)/Half.dll -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/Half/$(Configuration)/Half.lib -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/source/win_openexr/IlmBase/Half/*.h -> E:/lib/codes-openvdb/build/IlmBase/include
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/Iex/$(Configuration)/Iex.dll -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/Iex/$(Configuration)/Iex.lib -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/source/win_openexr/IlmBase/Iex/*.h -> E:/lib/codes-openvdb/build/IlmBase/include
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/IexMath/$(Configuration)/IexMath.dll -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/IexMath/$(Configuration)/IexMath.lib -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/source/win_openexr/IlmBase/IexMath/*.h -> E:/lib/codes-openvdb/build/IlmBase/include
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/Imath/$(Configuration)/Imath.dll -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/Imath/$(Configuration)/Imath.lib -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/source/win_openexr/IlmBase/Imath/*.h -> E:/lib/codes-openvdb/build/IlmBase/include
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/IlmThread/$(Configuration)/IlmThread.dll -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/build/IlmBase/IlmThread/$(Configuration)/IlmThread.lib -> E:/lib/codes-openvdb/build/IlmBase/lib
Packaging for /include: E:/lib/codes-openvdb/source/win_openexr/IlmBase/IlmThread/*.h -> E:/lib/codes-openvdb/build/IlmBase/include
Configuring done
Generating done
```
4.4. Go to the path \code\build\IlmBase, and open IlmBase.sln in VS2015<br>
4.5. Select "Release" mode, x64, and Build all.. Should have 12 succeeded, 0 failed.<br>

5) OpenEXR - CMake and Build   Here problems may occur 
-------------------------------------
5.1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\win_openexr\OpenEXR<br>
Build binaries: \codes\build\OpenEXR<br>
5.2. Click Configure, and choose "Visual Studio 14 2015 Win64". <br>
5.3. Now click Generate. <br>
You should see this output:<br>
```
Zlib Library: Found at E:/lib/codes-openvdb/build/zlib/lib/zlib.lib
IlmBase Library: Found at E:/lib/codes-openvdb/build/IlmBase/lib/Half.lib;E:/lib/codes-openvdb/build/IlmBase/lib/Iex.lib;E:/lib/codes-openvdb/build/IlmBase/lib/Imath.lib;E:/lib/codes-openvdb/build/IlmBase/lib/IlmThread.lib
ILMBASE_PACKAGE_PREFIX = E:/lib/codes-openvdb/build/IlmBase
Performing Test HAVE_GCC_INLINE_ASM_AVX
Performing Test HAVE_GCC_INLINE_ASM_AVX - Failed
Performing Test HAVE_SYSCONF_NPROCESSORS_ONLN
Performing Test HAVE_SYSCONF_NPROCESSORS_ONLN - Failed
Packaging for /include: E:/lib/codes-openvdb/source/win_openexr/OpenEXR/IlmImf/*.h -> E:/lib/codes-openvdb/build/OpenEXR/include
Configuring done
CMake Warning (dev) at IlmImf/CMakeLists.txt:189 (ADD_DEPENDENCIES):
  Policy CMP0046 is not set: Error on non-existent dependency in
  add_dependencies.  Run "cmake --help-policy CMP0046" for policy details.
  Use the cmake_policy command to set the policy and suppress this warning.

  The dependency target "b44ExpLogTable" of target "IlmImf" does not exist.
This warning is for project developers.  Use -Wno-dev to suppress it.

CMake Warning (dev) at IlmImf/CMakeLists.txt:198 (ADD_DEPENDENCIES):
  Policy CMP0046 is not set: Error on non-existent dependency in
  add_dependencies.  Run "cmake --help-policy CMP0046" for policy details.
  Use the cmake_policy command to set the policy and suppress this warning.

  The dependency target "dwaLookups" of target "IlmImf" does not exist.
This warning is for project developers.  Use -Wno-dev to suppress it.

Generating done
```
5.4. Go to the path \code\build\OpenEXR, and open OpenEXR.sln in VS2015<br>
5.5  Before build .sln,you should include zlib.h in IlmImf project's Properties pages then select VC++ Directories and add E:\lib\codes-openvdb\build\zlib\include   path to Include Directories
5.6. Select "Release" mode, 64-bit, and Build all.. Should have 15 succeeded, 0 failed.<br>

6) OpenVDB - CMake and Build  problems may occur in cmake process
-------------------------------------
6.1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\win_openvdb\openvdb<br>
Build binaries: \codes\build\OpenVDB<br>
6.2. Click Configure, and choose "Visual Studio 14 2015 Win64". <br>
6.3. Now click Generate. <br>
You should see this output:<br>
```
GLFW3 Library: Found at C:/Program Files (x86)/VC/lib/glfw3.lib
GLEW Library: Found at E:/lib/codes-openvdb/build/glew-1.11.0/lib/Release/x64/glew32.lib
CMake Error at E:/lib/codes-openvdb/source/win_openvdb/cmake_modules/FindBoost.cmake:1243 (message):
  Unable to find the requested Boost libraries.

  Boost version: 1.61.0

  Boost include path: E:/lib/codes-openvdb/build/boost_1_61_0

  Could not find the following Boost libraries:

          boost_iostreams
          boost_system
          boost_thread

  No Boost libraries were found.  You may need to set BOOST_LIBRARYDIR to the
  directory containing Boost libraries or BOOST_ROOT to the location of
  Boost.
Call Stack (most recent call first):
  CMakeLists.txt:57 (FIND_PACKAGE)


IlmBase Library: Found at E:/lib/codes-openvdb/build/IlmBase/lib/Half.lib;E:/lib/codes-openvdb/build/IlmBase/lib/Iex.lib;E:/lib/codes-openvdb/build/IlmBase/lib/Imath.lib;E:/lib/codes-openvdb/build/IlmBase/lib/IlmThread.lib
OpenEXR Library: Found at 
TBB Library Dir: E:/lib/codes-openvdb/build/tbb44/lib/intel64// 
TBB Arch:        intel64 
TBB Compiler:     
TBB Library: NOT Found! Confirm that TBB_ARCH_PLATFORM and TBB_INSTALL_DIR are correct.
CMake Error at E:/lib/codes-openvdb/source/win_openvdb/cmake_modules/FindTBB.cmake:296 (message):
  TBB Library: NOT Found! Confirm that TBB_ARCH_PLATFORM and TBB_INSTALL_DIR
  are correct.
Call Stack (most recent call first):
  CMakeLists.txt:61 (FIND_PACKAGE)


Configuring incomplete, errors occurred!
See also "E:/lib/codes-openvdb/build/OpenVDB/CMakeFiles/CMakeOutput.log".
```
6.4.Based on the above errors ,you should include path for NOTFOUND values including TBB and Boost in Name column
you can refer to this URL to see the path for TBB and Boost and glfw :
https://cloud.githubusercontent.com/assets/14851941/16338275/8d0b6d86-3a56-11e6-91ae-40299e2a474c.png

6.5. Go to the path \code\build\OpenVDB, and open OpenVDB.sln in VS2015<br>
6.6. Before building sln ,you should include zlib.h in project openvdb and include GL/glew.h in vdb_view project ,if the unresolved external symbol error of glfw occurs ,you should modify the path of glfw lib in corresponding project property pages ,c/c++ --General--Additional Include Directories
6.7. Select "Release" mode, 64-bit, and Build all.. Should have 6 succeeded, 1 failed.<br>

7) Check it and Try it!
-------------------------------------
This build system was designed to do everything automatically. When finished, you should have an openvdb.lib and openvdb.dll which you can use in other projects, and an vdb_render.exe which will confirm the build.<br>
Other things to observe:<br>
7.1. Upon success, the build folder should now look like this:<br>
```
\codes
  \build
    \boost_1_61_0
    \glew-1.11.0
    \glfw
    \IlmBase
    \OpenEXR
    \OpenVDB
    \tbb44
    \zlib
```
Of course, the \source should remain identical to what it was from github. It is not modified during build.<br>
7.2. You can also confirm correct packaging by looking the include and lib paths of \IlmBase and \OpenEXR.<br>
```
\IlmBase\include    Contains half.h, Iex.h, IlmThread.h, IlmBaseConfig.h, etc.
\IlmBase\lib        Contains Half.dll, Half.lib, Iex.dll, Iex.lib, IexMath.dll/lib, IlmThread.dll/lib, Imath.dll/lib
\OpenEXR\include    Contains many Imf*.h files, and OpenEXRConfig.h
\OpenEXR\lib        Contains IlmImf.dll and IlmImf.lib
```
7.3. The output of OpenVDB will be placed into \build\OpenVDB\Release<br>
Here you will find the openvdb.lib, vdb_print.exe and vdb_render.exe for testing, and copies of the supporting .dll and .lib files needed to run the binary<br>
```
vdb_render.exe      VDB Render sample, to confirm it all works.
vdb_print.exe       VDB Print sample
openvdb.lib         Link library for projects using openvdb
openvdb.dll         DLL library for projects using openvdb( actually  I  don't have this dll in release folder )
boost_system-vc140-mt-1_61.dll    DLL required for openvdb
Half.dll            DLL required for openvdb
Iex.dll             DLL required for openvdb
IlmImf.dll          DLL required for openvdb
IlmThread.dll       DLL required for openvdb
Imath.dll           DLL required for openvdb
tbb.dll             DLL required for openvdb
tbb_debug.dll       DLL required for openvdb
tbb_preview.dll     DLL required for openvdb
zlib.dll            DLL required for openvdb
(and possibly some others)
```
7.4. To finally test your OpenVDB build, download a sample .vdb file from samples at the bottom of this page:<br>
   http://www.openvdb.org/download/<br>
Place the .vdb file into the \build\OpenVDB\Release folder, next to vdb_render.exe<br>
7.5. From the command-line run this:<br>
\codes\build\OpenVDB\Release> vdb_render bunny_cloud.vdb bunny_cloud.exr -res 1920x1080 -translate 0,0,110 -absorb 0.4,0.2,0.1 -gain 0.2 -v

Build OpenVDB COOKBOOK Project
-------------------------------------
The win_openvdb fork also includes a project for the OpenVDB Cookbook example #1.<br>See: http://www.openvdb.org/documentation/doxygen/codeExamples.html<br>
The source is located in \win_openvdb\openvdb_cookbook.<br>
The following shows automated CMake steps for building this project. I would strongly recommend that you become familiar with CMake, as it will greatly simplify your life. Look at \openvdb_cookbook\CMakeLists.txt. It is a simple example that shows how each aspect of a build can be specified in a cmake. <br>
*NOTE* If you really want to create your own Visual Studio .sln and .vxproj, to really write the cookbook example from scratch, you can also do this. You must then explicitly set the correct include paths, input libraries, and preprocessor defines so that all the needed libraries are found.<br>
<br>
The steps for creating the Cookbook project art thou:<br>
1. Run cmake-gui, and specify the following source and build paths:<br>
Source code: \codes\source\win_openvdb\openvdb_cookbook<br>
Build binaries: \codes\build\openvdb_cookbook<br>
2. Click Configure, and choose "Visual Studio 14 2015 Win64". <br>
3. Now click Generate. <br>
4. Go to the path \code\build\openvdb_cookbook, and open OpenVDB_Cookbook.sln in VS2015<br>
5. Select "Debug" or "Release" mode, 64-bit, and Build all.<br>
6. Make sure that "openvdb_cookbook" project is set as the Startup project (right-click it and set as Startup Project)<br>
7. Run! <br>
*NOTE* If the console flashes briefly, this is correct. The sample does not wait for input on exit. To see it better, try Debug -> Start Without Debugging, or add a _getch() as the last line of  main()<br>
<br>
Troubleshooting:<br>
Q: What to do about LINK2038: mismatch detected for '_ITERATOR_DEBUG_LEVEL' value 'X' doesn't match value 'Y'<br>
A: For the cookbook project, this is usually caused building the OpenVDB.lib as a debug library instead of release. The cookbook expects to use the release version of openvdb.lib. If you would like to use OpenVDB.lib as debug, then you can modify the CMakelist.txt for openvdb_cookbook, find the line for _ITERATOR_DEBUG_LEVEL, and set it to '2' instead of '0'.<br>



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
