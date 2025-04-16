cmake_minimum_required(VERSION 3.10)

set(TARGET_NAME duplicates_test)

# 启用Qt自动MOC处理
set(CMAKE_AUTOMOC ON)

# 源文件
set(SOURCES
    main.cpp
)

# 创建可执行文件
add_executable(${TARGET_NAME} ${SOURCES})

# 查找Qt包
find_package(Qt5 COMPONENTS Core Network REQUIRED)

# 设置包含目录
target_include_directories(${TARGET_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

# 链接Qt库和qBreakpad库
target_link_libraries(${TARGET_NAME} PRIVATE
    Qt5::Core
    Qt5::Network
    qBreakpad::qBreakpad
)

# macOS特定配置
if(APPLE)
    find_library(APPKIT_LIBRARY AppKit)
    target_link_libraries(${TARGET_NAME} PRIVATE ${APPKIT_LIBRARY})
endif()

# 设置编译选项
target_compile_features(${TARGET_NAME} PRIVATE cxx_std_11)

# 设置警告选项（原始.pro文件中使用了warn_off）
if(MSVC)
    target_compile_options(${TARGET_NAME} PRIVATE /W1)
else()
    target_compile_options(${TARGET_NAME} PRIVATE -w)
endif()

# 确保输出目录与CMake默认行为一致（在Windows上明确设置）
if(WIN32)
    set_target_properties(${TARGET_NAME} PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    )
endif()
