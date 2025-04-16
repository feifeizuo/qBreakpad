cmake_minimum_required(VERSION 3.10)

# 按顺序构建子目录（与原始qmake中的CONFIG += ordered相同）
add_subdirectory(duplicates)
add_subdirectory(testsRunner)
