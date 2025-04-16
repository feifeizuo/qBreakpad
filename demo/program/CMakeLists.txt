cmake_minimum_required(VERSION 3.10)

set(TARGET_NAME test)

# 启用Qt自动MOC处理
set(CMAKE_AUTOMOC ON)

# 源文件
set(SOURCES
    TestThread.cpp
    main.cpp
)

# 头文件
set(HEADERS
    TestThread.h
)

# 创建可执行文件
add_executable(${TARGET_NAME} ${SOURCES} ${HEADERS})

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

# macOS特定配置（从原始.pro文件中提取）
if(APPLE)
    find_library(APPKIT_LIBRARY AppKit)
    target_link_libraries(${TARGET_NAME} PRIVATE ${APPKIT_LIBRARY})
endif()

# 设置编译选项
target_compile_features(${TARGET_NAME} PRIVATE cxx_std_11)

# 安装配置
install(TARGETS ${TARGET_NAME}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
