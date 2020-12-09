#TangoTest
## Installing TangoTest on your Windows
TangoTest.exe in this repostory was taken from regular Tango windows installation described here:
https://tango-controls.readthedocs.io/en/latest/installation/tango-on-windows.html
## Building TangoTest on your own

Before building TangoTest you have to install tango.

First define the variables (change to the currently installed version): 
```bat
SET TANGO_PKG_LIBRARY_DIRS="C:\projects\cppTango-9.3.3\lib"
SET TANGO_PKG_INCLUDE_DIRS="C:\projects\cppTango-9.3.3\include;C:\projects\cppTango-9.3.3\include\omniORB4;C:\projects\cppTango-9.3.3\include\omnithread"
SET TANGO_PKG_CFLAGS_OTHER="/machine:x64;/force:multiple"
SET TANGO_PKG_LIBRARIES="tango;omniORB4"
```
Next, we have to change the CMakeLists.txt a little, because its using pkg_search_module, which is not available for windows.
Just remove line ```pkg_search_module(TANGO_PKG REQUIRED tango)``` and we will just have to pass the variables manually to cmake by -D flag.
Dscription of the variables is here: https://cmake.org/cmake/help/latest/module/FindPkgConfig.html#command:pkg_check_modules

Next, we build.
```bat
git clone https://github.com/tango-controls/TangoTest.git
cd TangoTest
REM build and install TangoTest
cd C:\projects\TangoTest
md build
cd C:\projects\TangoTest\build
REM IMPORTANT: you have to use the same compiler that was used to compile Tango, because some preprocessor variables HAS_LongDouble are set for G++ but not for MSV compiler. then the mismatch will occur an there will be lacking class LongDoubleSeq definition
cmake .. -DTANGO_PKG_LIBRARY_DIRS=%TANGO_PKG_LIBRARY_DIRS% -DTANGO_PKG_INCLUDE_DIRS=%TANGO_PKG_INCLUDE_DIRS% -DTANGO_PKG_CFLAGS_OTHER=%TANGO_PKG_CFLAGS_OTHER% -DTANGO_PKG_LIBRARIES=%TANGO_PKG_LIBRARIES%
cmake --build . --target TangoTest --config Release
```

Unfortunalety when I tried this I got error: 
```
Generating Code...
omniORB4.lib(GIOP_S.o) : error LNK2038: mismatch detected for 'RuntimeLibrary': value 'MT_StaticRelease' doesn't match value 'MD_DynamicRelease' in ClassFactory.obj [C:\projects\TangoTest\build\TangoTest.vcxproj]
```
According to this: https://stackoverflow.com/questions/28887001/lnk2038-mismatch-detected-for-runtimelibrary-value-mt-staticrelease-doesn/28887127 I tried to compile the executable statically and adding:
```cmake
SET(CMAKE_EXE_LINKER_FLAGS "-static")
REM link_libraries("-static")
```

But the error didnt go away. Theres two ways of compiling tango - dynamic and static and theres a flag to change it and maybe there mismatch