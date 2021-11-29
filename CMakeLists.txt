cmake_minimum_required(VERSION 3.16)
project(snap7)

set(CMAKE_CXX_STANDARD 20)

find_package(Threads REQUIRED)

IF ( BUILD_SHARED )
    set(LIBRARY_TYPE SHARED)
ELSE ()
    set(LIBRARY_TYPE STATIC)
ENDIF ()

# Core files
set(core_SOURCES
        core/s7_client.cpp
        core/s7_isotcp.cpp
        core/s7_micro_client.cpp
        core/s7_partner.cpp
        core/s7_peer.cpp
        core/s7_server.cpp
        core/s7_text.cpp
        )

set(core_HEADERS
        core/s7_client.h
        core/s7_firmware.h
        core/s7_isotcp.h
        core/s7_micro_client.h
        core/s7_partner.h
        core/s7_peer.h
        core/s7_server.h
        core/s7_text.h
        core/s7_types.h
        )

# System files
set(sys_SOURCES
        sys/snap_msgsock.cpp
        sys/snap_sysutils.cpp
        sys/snap_tcpsrvr.cpp
        sys/snap_threads.cpp
        )

set(sys_HEADERS
        sys/snap_msgsock.h
        sys/snap_platform.h
        sys/snap_sysutils.h
        sys/snap_tcpsrvr.h
        sys/snap_threads.h
        )

IF ( UNIX )
    list(APPEND sys_HEADERS sys/unix_threads.h)
ENDIF ()

# Library files
set(lib_SOURCES lib/snap7_libmain.cpp)
set(lib_HEADERS lib/snap7_libmain.h)

# Dependencies
set(DEPENDENCIES)
IF (UNIX)
    list(APPEND DEPENDENCIES rt)
ENDIF ()

# includes
include_directories(core/)
include_directories(sys/)
include_directories(lib/)

# remove dumb warnings
IF (MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    add_definitions(-D_WINSOCK_DEPRECATED_NO_WARNINGS)
ENDIF ()

# Build the library
add_library(snap7
        ${LIBRARY_TYPE}
        ${core_SOURCES}
        ${core_HEADERS}
        ${sys_SOURCES}
        ${sys_HEADERS}
        ${lib_SOURCES}
        ${lib_HEADERS}
        ${lib_DEFINITIONS}
        )

target_link_libraries(snap7 ${CMAKE_THREAD_LIBS_INIT} ${DEPENDENCIES})

# install library and headers
install(TARGETS snap7
        RUNTIME DESTINATION lib
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        )

install(FILES
        ${core_HEADERS}
        ${sys_HEADERS}
        ${lib_HEADERS}
        DESTINATION
        include/snap7
        )
