cmake_minimum_required(VERSION 3.10)

set(TARGET_NAME ReporterExample)

# 启用Qt自动MOC处理
set(CMAKE_AUTOMOC ON)
# 启用Qt自动UI处理
set(CMAKE_AUTOUIC ON)

# 源文件
set(SOURCES
    reporter.cpp
    ../program/TestThread.cpp
)

# 头文件
set(HEADERS
    reporter.h
    ../program/TestThread.h
)

# UI文件
set(UI_FILES
    reporter.ui
)

# 查找Qt包
find_package(Qt5 COMPONENTS Core Network REQUIRED)

# 对于Qt5，需要添加Widgets组件
find_package(Qt5 COMPONENTS Widgets REQUIRED)

# 处理Qt UI文件
qt5_wrap_ui(UI_HEADERS ${UI_FILES})

# 创建可执行文件
add_executable(${TARGET_NAME} ${SOURCES} ${HEADERS} ${UI_HEADERS})

# 设置包含目录
target_include_directories(${TARGET_NAME} PRIVATE 
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/../program
    ${CMAKE_CURRENT_BINARY_DIR}  # 用于访问ui_*.h文件
)

# 链接Qt库和qBreakpad库
target_link_libraries(${TARGET_NAME} PRIVATE
    Qt5::Core
    Qt5::Network
    Qt5::Widgets
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
