cmake_minimum_required(VERSION 3.10)

set(TARGET_NAME testsRunner)

# 启用Qt自动MOC处理
set(CMAKE_AUTOMOC ON)

# 源文件
set(SOURCES
    tst_duplicates.cpp
)

# 创建可执行文件
add_executable(${TARGET_NAME} ${SOURCES})

# 查找Qt包
find_package(Qt5 COMPONENTS Core Test REQUIRED)

# 设置包含目录
target_include_directories(${TARGET_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

# 链接Qt库
target_link_libraries(${TARGET_NAME} PRIVATE
    Qt5::Core
    Qt5::Test
)

# 设置编译选项
target_compile_features(${TARGET_NAME} PRIVATE cxx_std_11)

# 设置测试程序路径定义
if(WIN32)
    set_target_properties(${TARGET_NAME} PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    )
    target_compile_definitions(${TARGET_NAME} PRIVATE 
        "TEST_RUNNER=\"${CMAKE_CURRENT_BINARY_DIR}/../duplicates/duplicates_test.exe\""
    )
else()
    target_compile_definitions(${TARGET_NAME} PRIVATE 
        "TEST_RUNNER=\"${CMAKE_CURRENT_BINARY_DIR}/../duplicates/duplicates_test\""
    )
endif()

# 添加测试
add_test(NAME ${TARGET_NAME} COMMAND ${TARGET_NAME})
