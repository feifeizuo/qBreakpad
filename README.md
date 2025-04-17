#qBreakpad

[![Build status](https://travis-ci.org/buzzySmile/qBreakpad.svg?branch=master)](https://travis-ci.org/buzzySmile/qBreakpad)

[中文文档](README_zh.md)

qBreakpad is Qt library to use google-breakpad crash reporting facilities (and using it conviniently).
Supports
* Windows (but crash dump decoding will not work with MinGW compiler)
* Linux
* MacOS X

How to use
----------------
* Clone repository recursively
```bash
$ git clone --recursive https://github.com/buzzySmile/qBreakpad.git
```

* Build qBreakpad library using CMake
```bash
$ mkdir build && cd build
$ cmake ..
$ cmake --build .
```

* Find and link qBreakpad in your CMake project (after installation)
```cmake
# In your CMakeLists.txt
find_package(qBreakpad REQUIRED)
target_link_libraries(your_target PRIVATE qBreakpad::qBreakpad)
```

* Include qBreakpad as a subproject in your CMake project
```cmake
add_subdirectory(path/to/qBreakpad)
target_link_libraries(your_target PRIVATE qBreakpad::qBreakpad)
```

* Use FetchContent to integrate qBreakpad directly (Modern CMake, recommended)
```cmake
include(FetchContent)

FetchContent_Declare(
  qBreakpad
  GIT_REPOSITORY https://github.com/feifeizuo/qBreakpad.git
  GIT_TAG        master  # or specify a specific tag/commit
)

FetchContent_MakeAvailable(qBreakpad)

# Use the target directly, no need to manually set include paths or link
target_link_libraries(your_target PRIVATE qBreakpad::qBreakpad)
```

* Use ```QBreakpadHandler``` singleton class to enable automatic crash dumps generation on any failure; example:
```c++
#include <QBreakpadHandler.h>

int main(int argc, char* argv[])
{
    ...
    QBreakpadInstance.setDumpPath(QLatin1String("crashes"));
    ...
}
```

* Read Google Breakpad documentation to know further workflow

Getting started with Google Breakpad
----------------
https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/getting_started_with_breakpad.md

Detail description about integration `qBreakpad` into your system and platform you could find in **[Wiki](https://github.com/buzzySmile/qBreakpad/wiki)**.
