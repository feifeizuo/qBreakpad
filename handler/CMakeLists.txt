set(TARGET_NAME qBreakpad)

# 启用Qt自动MOC处理
set(CMAKE_AUTOMOC ON)

# Source files
set(SOURCES
    QBreakpadHandler.cpp
    QBreakpadHttpUploader.cpp
)

# Header files
set(HEADERS
    singletone/call_once.h
    singletone/singleton.h
    QBreakpadHandler.h
    QBreakpadHttpUploader.h
)

# Define target
add_library(${TARGET_NAME} ${SOURCES} ${HEADERS})

# Add alias with namespace for use from other parts of the project
add_library(qBreakpad::qBreakpad ALIAS ${TARGET_NAME})

# Set include directories
target_include_directories(${TARGET_NAME} 
    PUBLIC 
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/..>
        $<INSTALL_INTERFACE:include>
    PRIVATE
        ${BREAKPAD_PATH}
)

# Google Breakpad platform-specific sources
if(UNIX)
    target_sources(${TARGET_NAME} PRIVATE
        ${BREAKPAD_PATH}/client/minidump_file_writer.cc
        ${BREAKPAD_PATH}/common/convert_UTF.cc
        ${BREAKPAD_PATH}/common/md5.cc
        ${BREAKPAD_PATH}/common/string_conversion.cc
    )
endif()

if(APPLE)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ObjC++")
    target_sources(${TARGET_NAME} PRIVATE
        ${BREAKPAD_PATH}/client/mac/crash_generation/crash_generation_client.cc
        ${BREAKPAD_PATH}/client/mac/handler/breakpad_nlist_64.cc
        ${BREAKPAD_PATH}/client/mac/handler/dynamic_images.cc
        ${BREAKPAD_PATH}/client/mac/handler/exception_handler.cc
        ${BREAKPAD_PATH}/client/mac/handler/minidump_generator.cc
        ${BREAKPAD_PATH}/common/mac/MachIPC.mm
        ${BREAKPAD_PATH}/common/mac/bootstrap_compat.cc
        ${BREAKPAD_PATH}/common/mac/file_id.cc
        ${BREAKPAD_PATH}/common/mac/macho_id.cc
        ${BREAKPAD_PATH}/common/mac/macho_utilities.cc
        ${BREAKPAD_PATH}/common/mac/macho_walker.cc
        ${BREAKPAD_PATH}/common/mac/string_utilities.cc
    )
    target_link_libraries(${TARGET_NAME} PRIVATE crypto)
endif()

if(UNIX AND NOT APPLE)
    target_sources(${TARGET_NAME} PRIVATE
        ${BREAKPAD_PATH}/client/linux/crash_generation/crash_generation_client.cc
        ${BREAKPAD_PATH}/client/linux/dump_writer_common/thread_info.cc
        ${BREAKPAD_PATH}/client/linux/dump_writer_common/ucontext_reader.cc
        ${BREAKPAD_PATH}/client/linux/handler/exception_handler.cc
        ${BREAKPAD_PATH}/client/linux/handler/minidump_descriptor.cc
        ${BREAKPAD_PATH}/client/linux/log/log.cc
        ${BREAKPAD_PATH}/client/linux/microdump_writer/microdump_writer.cc
        ${BREAKPAD_PATH}/client/linux/minidump_writer/linux_core_dumper.cc
        ${BREAKPAD_PATH}/client/linux/minidump_writer/linux_dumper.cc
        ${BREAKPAD_PATH}/client/linux/minidump_writer/linux_ptrace_dumper.cc
        ${BREAKPAD_PATH}/client/linux/minidump_writer/minidump_writer.cc
        ${BREAKPAD_PATH}/common/linux/breakpad_getcontext.S
        ${BREAKPAD_PATH}/common/linux/elfutils.cc
        ${BREAKPAD_PATH}/common/linux/file_id.cc
        ${BREAKPAD_PATH}/common/linux/guid_creator.cc
        ${BREAKPAD_PATH}/common/linux/linux_libc_support.cc
        ${BREAKPAD_PATH}/common/linux/memory_mapped_file.cc
        ${BREAKPAD_PATH}/common/linux/safe_readlink.cc
    )
endif()

if(WIN32)
    target_sources(${TARGET_NAME} PRIVATE
        ${BREAKPAD_PATH}/client/windows/crash_generation/crash_generation_client.cc
        ${BREAKPAD_PATH}/client/windows/handler/exception_handler.cc
        ${BREAKPAD_PATH}/common/windows/guid_string.cc
    )
endif()

# Link Qt libraries
target_link_libraries(${TARGET_NAME}
    PUBLIC
        Qt5::Core
        Qt5::Network
)

# Set compile options (matching those in handler.pro)
target_compile_options(${TARGET_NAME} PRIVATE
    $<$<CXX_COMPILER_ID:MSVC>:/W3>
    $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra>
)

# Set properties
set_target_properties(${TARGET_NAME} PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
    EXPORT_NAME qBreakpad
)

# Installation
install(TARGETS ${TARGET_NAME}
    EXPORT qBreakpadTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# Install header files
install(FILES ${HEADERS}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/qBreakpad
)
