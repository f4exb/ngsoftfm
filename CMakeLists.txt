# CMake definitions for SoftFM

cmake_minimum_required(VERSION 3.0.2)
project(SoftFM)

find_package(Threads)
find_package(PkgConfig)
find_package(ALSA REQUIRED)
find_package(Boost 1.47)

# Find Airspy library.
pkg_check_modules(PKG_AIRSPY libairspy)
find_path(LIBAIRSPY_INCLUDE_DIR airspy.h
          HINT ${PKG_LIBAIRSPY_INCLUDE_DIRS})
find_library(LIBAIRSPY_LIBRARIES libairspy.a
          HINT ${PKG_LIBAIRSPY_LIBRARIES_DIRS})

# Find BladeRF library.
pkg_check_modules(PKG_BLADERF libbladerf)
find_path(LIBBLADERF_INCLUDE_DIR libbladeRF.h
          HINT ${PKG_LIBBLADERF_INCLUDE_DIR})
find_library(LIBBLADERF_LIBRARIES libbladeRF.so
          HINT ${PKG_LIBBLADERF_LIBRARIES_DIRS})

# Find HackRF library.
pkg_check_modules(PKG_HACKRF libhackrf)
find_path(LIBHACKRF_INCLUDE_DIR hackrf.h
          HINT ${PKG_LIBHACKRF_INCLUDE_DIRS})
find_library(LIBHACKRF_LIBRARIES libhackrf.a
          HINT ${PKG_LIBHACKRF_LIBRARIES_DIRS})

# Find RTL-SDR library.
pkg_check_modules(PKG_RTLSDR librtlsdr)
find_path(LIBRTLSDR_INCLUDE_DIR rtl-sdr.h
          HINT ${PKG_LIBRTLSDR_INCLUDE_DIRS})
find_library(LIBRTLSDR_LIBRARIES librtlsdr.a
          HINT ${PKG_LIBRTLSDR_LIBRARIES_DIRS})

# Find libusb
pkg_check_modules(PKG_LIBUSB libusb-1.0)
find_path(LIBUSB_INCLUDE_DIR libusb.h
          HINT ${PKG_LIBUSB_INCLUDE_DIRS}
          PATH_SUFFIXES libusb-1.0)
find_library(LIBUSB_LIBRARY usb-1.0
             HINT ${PKG_LIBUSB_LIBRARY_DIRS})

if(LIBRTLSDR_INCLUDE_DIR AND LIBRTLSDR_LIBRARIES)
    message(STATUS "Found librtlsdr: ${LIBRTLSDR_INCLUDE_DIR}, ${LIBRTLSDR_LIBRARIES}")
else()
    message(WARNING "Can not find Osmocom RTL-SDR library")
    message("Try again with environment variable PKG_CONFIG_PATH")
    message("or with -DLIBRTLSDR_INCLUDE_DIR=/path/rtlsdr/include")
    message("        -DLIBRTLSDR_LIBRARIES=/path/rtlsdr/lib/librtlsdr.so")
endif()

if(LIBAIRSPY_INCLUDE_DIR AND LIBAIRSPY_LIBRARIES)
    message(STATUS "Found libairspy: ${LIBAIRSPY_INCLUDE_DIR}, ${LIBAIRSPY_LIBRARIES}")
else()
    message(WARNING "Can not find libairspy library")
    message("Try again with environment variable PKG_CONFIG_PATH")
    message("or with -DLIBAIRSPY_INCLUDE_DIR=/path/libairspy/include")
    message("        -DLIBAIRSPY_LIBRARIES=/path/libairspy/lib/libairspy.so")
endif()

if(LIBBLADERF_INCLUDE_DIR AND LIBBLADERF_LIBRARIES)
    message(STATUS "Found libbladeRF: ${LIBBLADERF_INCLUDE_DIR}, ${LIBBLADERF_LIBRARIES}")
else()
    message(WARNING "Can not find libbladeRF library")
    message("Try again with environment variable PKG_CONFIG_PATH")
    message("or with -DLIBBLADERF_INCLUDE_DIR=/path/libbladeRF/include")
    message("        -DLIBBLADERF_LIBRARIES=/path/libbladeRF/lib/libbladeRF.so")
endif()

if(LIBHACKRF_INCLUDE_DIR AND LIBHACKRF_LIBRARIES)
    message(STATUS "Found libhackrf: ${LIBHACKRF_INCLUDE_DIR}, ${LIBHACKRF_LIBRARIES}")
else()
    message(WARNING "Can not find libhackrf library")
    message("Try again with environment variable PKG_CONFIG_PATH")
    message("or with -DLIBHACKRF_INCLUDE_DIR=/path/libhackrf/include")
    message("        -DLIBHACKRF_LIBRARIES=/path/libhackrf/lib/libbladeRF.so")
endif()

set(LIBAIRSPY_INCLUDE_DIRS ${LIBAIRSPY_INCLUDE_DIR} ${LIBUSB_INCLUDE_DIR})
set(LIBAIRSPY_LIBRARIES    ${LIBAIRSPY_LIBRARIES} ${LIBUSB_LIBRARY})

set(LIBBLADERF_INCLUDE_DIRS ${LIBBLADERF_INCLUDE_DIR} ${LIBUSB_INCLUDE_DIR})
set(LIBBLADERF_LIBRARIES    ${LIBBLADERF_LIBRARIES} ${LIBUSB_LIBRARY})

set(LIBHACKRF_INCLUDE_DIRS ${LIBHACKRF_INCLUDE_DIR} ${LIBUSB_INCLUDE_DIR})
set(LIBHACKRF_LIBRARIES    ${LIBHACKRF_LIBRARIES} ${LIBUSB_LIBRARY})

set(LIBRTLSDR_INCLUDE_DIRS ${LIBRTLSDR_INCLUDE_DIR} ${LIBUSB_INCLUDE_DIR})
set(LIBRTLSDR_LIBRARIES    ${LIBRTLSDR_LIBRARIES} ${LIBUSB_LIBRARY} ${ZEROMQ_LIBRARIES})

# Compiler flags.
set(CMAKE_CXX_FLAGS "-Wall -std=c++11 -O2 -ffast-math -ftree-vectorize ${EXTRA_FLAGS}")

set(sfmbase_SOURCES
    sfmbase/Filter.cpp
    sfmbase/FmDecode.cpp
    sfmbase/AudioOutput.cpp 
)

set(sfmbase_HEADERS
    include/AudioOutput.h
    include/Filter.h
    include/FmDecode.h
    include/MovingAverage.h
    include/Source.h
    include/SoftFM.h
    include/DataBuffer.h
    include/fastatan2.h
    include/parsekv.h
    include/util.h
)

# Base sources

set(sfmbase_SOURCES
    ${sfmbase_SOURCES}
    ${sfmbase_HEADERS}
)

# RTL-SDR sources

set(sfmrtlsdr_SOURCES
    sfmbase/RtlSdrSource.cpp
)

set(sfmrtlsdr_HEADERS
    include/RtlSdrSource.h
)

set(sfmrtlsdr_SOURCES
    ${sfmrtlsdr_SOURCES}
    ${sfmrtlsdr_HEADERS}
)

# HackRF sources

set(sfmhackrf_SOURCES
    sfmbase/HackRFSource.cpp
)

set(sfmhackrf_HEADERS
    include/HackRFSource.h
)

set(sfmhackrf_SOURCES
    ${sfmhackrf_SOURCES}
    ${sfmhackrf_HEADERS}
)

# Airspy sources

set(sfmairspy_SOURCES
    sfmbase/AirspySource.cpp
)

set(sfmairspy_HEADERS
    include/AirspySource.h
)

set(sfmairspy_SOURCES
    ${sfmairspy_SOURCES}
    ${sfmairspy_HEADERS}
)

# BLadeRF sources

set(sfmbladerf_SOURCES
    sfmbase/BladeRFSource.cpp
)

set(sfmbladerf_HEADERS
    include/BladeRFSource.h
)

set(sfmbladerf_SOURCES
    ${sfmbladerf_SOURCES}
    ${sfmbladerf_HEADERS}
)

# Libraries

add_library(sfmbase STATIC
    ${sfmbase_SOURCES}
)

add_library(sfmrtlsdr STATIC
    ${sfmrtlsdr_SOURCES}
)

add_library(sfmhackrf STATIC
    ${sfmhackrf_SOURCES}
)

add_library(sfmairspy STATIC
    ${sfmairspy_SOURCES}
)

add_library(sfmbladerf STATIC
    ${sfmbladerf_SOURCES}
)

add_executable(softfm
	main.cpp
)

include_directories(
    ${CMAKE_SOURCE_DIR}/include
    ${ALSA_INCLUDE_DIRS}
    ${EXTRA_INCLUDES} 
)

target_link_libraries(softfm
    sfmbase
    sfmrtlsdr
    sfmhackrf
    sfmairspy
    sfmbladerf
    ${CMAKE_THREAD_LIBS_INIT}
    ${ALSA_LIBRARIES}
    ${EXTRA_LIBS} 
)

target_include_directories(sfmrtlsdr PUBLIC
    ${LIBRTLSDR_INCLUDE_DIRS}
)

target_include_directories(sfmhackrf PUBLIC
    ${LIBHACKRF_INCLUDE_DIRS}
)

target_include_directories(sfmairspy PUBLIC
    ${LIBAIRSPY_INCLUDE_DIRS}
)

target_include_directories(sfmbladerf PUBLIC
    ${LIBBLADERF_INCLUDE_DIRS}
)

target_link_libraries(sfmrtlsdr
    ${LIBRTLSDR_LIBRARIES}
)

target_link_libraries(sfmhackrf
    ${LIBHACKRF_LIBRARIES}
)

target_link_libraries(sfmairspy
    ${LIBAIRSPY_LIBRARIES}
)

target_link_libraries(sfmbladerf
    ${LIBBLADERF_LIBRARIES}
)

install(TARGETS softfm DESTINATION bin)
install(TARGETS sfmbase sfmrtlsdr sfmhackrf sfmairspy sfmbladerf DESTINATION lib)
