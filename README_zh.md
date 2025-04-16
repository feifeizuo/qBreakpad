# qBreakpad

[![构建状态](https://travis-ci.org/buzzySmile/qBreakpad.svg?branch=master)](https://travis-ci.org/buzzySmile/qBreakpad)

[English Document](README.md)

qBreakpad是一个用于集成Google Breakpad崩溃报告功能的Qt库（便于方便地使用）。

支持的平台：
* Windows（注意：使用MinGW编译器将无法进行崩溃转储解码）
* Linux
* MacOS X

使用方法
----------------
* 递归克隆仓库
```bash
$ git clone --recursive https://github.com/buzzySmile/qBreakpad.git
```

* 使用CMake构建qBreakpad库
```bash
$ mkdir build && cd build
$ cmake ..
$ cmake --build .
```

* 在您的CMake项目中查找并链接qBreakpad库
```cmake
# 在您的CMakeLists.txt中添加
find_package(qBreakpad REQUIRED)
target_link_libraries(your_target PRIVATE qBreakpad::qBreakpad)
```

* 或者，您可以将qBreakpad作为子项目包含在您的CMake项目中
```cmake
add_subdirectory(path/to/qBreakpad)
target_link_libraries(your_target PRIVATE qBreakpad::qBreakpad)
```

* 使用`QBreakpadHandler`单例类来启用自动崩溃转储生成功能；示例：
```c++
#include <QBreakpadHandler.h>

int main(int argc, char* argv[])
{
    ...
    QBreakpadInstance.setDumpPath(QLatin1String("crashes"));
    ...
}
```

* 阅读Google Breakpad文档了解更多工作流程

开始使用Google Breakpad
----------------
https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/getting_started_with_breakpad.md

有关将`qBreakpad`集成到您的系统和平台的详细说明，您可以在**[Wiki](https://github.com/buzzySmile/qBreakpad/wiki)**中找到。
