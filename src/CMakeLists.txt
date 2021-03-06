# ---------------------------------------------------------------------
# pion:  a Boost C++ framework for building lightweight HTTP interfaces
# ---------------------------------------------------------------------
# Copyright (C) 2013 Splunk Inc.  (https://github.com/splunk/pion)
#
# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt
# ---------------------------------------------------------------------

cmake_minimum_required(VERSION 2.8.10 FATAL_ERROR)

set(PROJECT_NAME pion)
project(${PROJECT_NAME} CXX)

# get all *.cpp files from src to build target
file(GLOB SRC_FILES ${PROJECT_SOURCE_DIR}/*.cpp)
file(GLOB_RECURSE HDR_FILES ${PROJECT_WIDE_INCLUDE}/*.hpp)

if (NOT BUILD_SPDY)
    list(REMOVE_ITEM SRC_FILES ${PROJECT_SOURCE_DIR}/spdy_decompressor.cpp)
    list(REMOVE_ITEM SRC_FILES ${PROJECT_SOURCE_DIR}/spdy_parser.cpp)
    list(REMOVE_ITEM HDR_FILES ${PROJECT_WIDE_INCLUDE}/pion/spdy/decompressor.hpp)
    list(REMOVE_ITEM HDR_FILES ${PROJECT_WIDE_INCLUDE}/pion/spdy/parser.hpp)
    list(REMOVE_ITEM HDR_FILES ${PROJECT_WIDE_INCLUDE}/pion/spdy/types.hpp)
endif()

if(MSVC)
    if(BUILD_SHARED_LIBS)
        add_definitions(-DPION_EXPORTS -D_USRDLL)
    else()
        add_definitions(-D_LIB)
    endif()
endif()
# build target
add_library(${PROJECT_NAME} ${SRC_FILES} ${HDR_FILES})

target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES})
if (ZLIB_FOUND)
    target_link_libraries(${PROJECT_NAME} ${ZLIB_LIBRARIES})
endif()
if (BZIP2_FOUND )
    target_link_libraries(${PROJECT_NAME} ${BZIP2_LIBRARIES})
endif()
if (OPENSSL_FOUND )
    target_link_libraries(${PROJECT_NAME} ${OPENSSL_LIBRARIES})
endif()
if (LOG4CPLUS_FOUND )
    target_link_libraries(${PROJECT_NAME} ${LOG4CPLUS_LIBRARIES})
endif()

install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
)

include(CMakePackageConfigHelpers)
configure_file(${CMAKE_SOURCE_DIR}/cmake/modules/lib${PROJECT_NAME}-config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/lib${PROJECT_NAME}-config.cmake
    @ONLY)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/lib${PROJECT_NAME}-config-version.cmake
    VERSION ${PION_VERSION}
    COMPATIBILITY AnyNewerVersion)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/lib${PROJECT_NAME}-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/lib${PROJECT_NAME}-config-version.cmake
    DESTINATION lib/cmake/lib${PROJECT_NAME}-${PION_VERSION})
