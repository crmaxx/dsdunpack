# DSD Unpack - https://github.com/crmaxx/dsdunpack
#
# Copyright (c) 2014 by Michael Burton.
# Copyright (c) 2024 by Maxim Zhukov.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA

cmake_minimum_required (VERSION 3.8)
project(dsdtools C)

if(NOT CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    message( FATAL_ERROR "Need Variable-length automatic arrays from c99." )
endif()

SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-deprecated-non-prototype")
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-unused-command-line-argument")
# SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-unused-but-set-variable")

# Macros we'll need
include(CheckIncludeFile)
include(CheckFunctionExists)
include(CheckTypeSize)
include(FindThreads)

# Include directory paths
include_directories(${dsdtools_SOURCE_DIR})

include_directories("./lib/libdstdec")
# MSVC doesn't come with a unistd.h
include_directories("./lib/unistd_h")
include_directories("./lib/c-cuelib")

# Extra flags for GCC
if (CMAKE_COMPILER_IS_GNUCC)
  add_definitions(
      -pipe
      -Wall -Wextra -Wcast-align -Wpointer-arith
      -Wno-unused-parameter
      -mno-ms-bitfields # breaks __attribute__((packed)) on Windows
      -msse2)
endif (CMAKE_COMPILER_IS_GNUCC)

if (MSVC)
    find_package(PThreads4W REQUIRED)

    set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} /MDd")
    set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /MD /GL /arch:AVX2 /D PTW32_STATIC_LIB")
    add_definitions(-D_CRT_NONSTDC_NO_DEPRECATE)
    add_definitions(-D_CRT_SECURE_NO_DEPRECATE)
    add_definitions(-D_CRT_NONSTDC_NO_WARNINGS)
    
    # MSVC doesn't come with a getopt implementation
    include_directories("./lib/getopt")
    file(GLOB getopt_headers ./lib/getopt/*.h)
    file(GLOB getopt_sources ./lib/getopt/*.c)
    source_group(getopt FILES ${getopt_headers} ${getopt_sources})
else()
    add_definitions(-D_FILE_OFFSET_BITS=64)
    set(CMAKE_C_STANDARD_LIBRARIES "${CMAKE_C_STANDARD_LIRARIES} -lpthread")
endif()

file(GLOB libdstdec_headers ./lib/libdstdec/*.h)
file(GLOB libdstdec_sources ./lib/libdstdec/*.c)
source_group(libdstdec FILES ${libdstdec_headers} ${libdstdec_sources})

file(GLOB unistd_h_header ./lib/libdstdec/unistd.h)
source_group(unistd_h FILES ${unistd_h_header})

file(GLOB c_cuelib_headers ./lib/c-cuelib/*.h)
file(GLOB c_cuelib_sources ./lib/c-cuelib/*.c)
source_group(c_cuelib FILES ${c_cuelib_headers} ${c_cuelib_sources})

file(GLOB main_headers ./*.h)
file(GLOB main_sources ./*.c)
source_group(main FILES ${main_headers} ${main_sources})

add_executable(dsdtools 
    ${main_headers} ${main_sources}
    ${libdstdec_headers} ${libdstdec_sources}
    ${unistd_h}
    ${c_cuelib_headers} ${c_cuelib_sources}
	${getopt_headers} ${getopt_sources}
    )

if (MSVC)
    target_link_libraries(dsdtools PRIVATE PThreads4W::PThreads4W)
endif()

# For Variable-length automatic arrays
set_property(TARGET dsdtools PROPERTY C_STANDARD 99)

set(CMAKE_BUILD_TYPE Release)
