# CMake file for SDL build of SimCoupe

cmake_minimum_required(VERSION 2.6)

project(simcoupe)

set(CMAKE_BUILD_TYPE Release)

set(USE_OPENGL YES)

include_directories(Base/ SDL/)

# Recommend native Win32/Mac building as they're not well supported yet
if (APPLE)
  message(WARNING "Please consider using Xcode for Mac OS X builds")
elseif (WIN32)
  message(WARNING "Please consider using Visual Studio for Windows builds")
endif ()

file(GLOB BASE_SRC Base/*.cpp Base/*.c)
file(GLOB SDL_SRC SDL/*.cpp)

find_package(SDL REQUIRED)
include_directories(${SDL_INCLUDE_DIR})
link_libraries(${SDL_LIBRARY})

if (USE_OPENGL)
  find_package(OpenGL REQUIRED)
  message(STATUS "Using OpenGL")
  add_definitions(-DUSE_OPENGL)
  include_directories(${OPENGL_INCLUDE_DIR})
  link_libraries(${OPENGL_LIBRARY})
endif (USE_OPENGL)

find_package(ZLIB REQUIRED)
if (ZLIB_FOUND)
  message(STATUS "Using Zlib")
  add_definitions(-DUSE_ZLIB)
  include_directories(${ZLIB_INCLUDE_DIR})
  link_libraries(${ZLIB_LIBRARY})
endif (ZLIB_FOUND)

find_package(BZip2)
if (BZIP2_FOUND)
  message(STATUS "Using BZip2")
  add_definitions(-DUSE_BZIP2)
  include_directories(${BZIP2_INCLUDE_DIR})
  link_libraries(${BZIP2_LIBRARIES})
endif (BZIP2_FOUND)

find_library(RESID_LIBRARY NAMES resid ENV LD_LIBRARY_PATH)
find_path(RESID_INCLUDE_DIR resid/sid.h)
if (RESID_LIBRARY AND RESID_INCLUDE_DIR)
  message(STATUS "Using reSID")
  add_definitions(-DUSE_RESID)
  include_directories(${RESID_INCLUDE_DIR})
  link_libraries(${RESID_LIBRARY})
endif (RESID_LIBRARY AND RESID_INCLUDE_DIR)

if (CMAKE_BUILD_TYPE MATCHES Debug)
  add_definitions(-D_DEBUG)
endif (CMAKE_BUILD_TYPE MATCHES Debug)

add_executable(simcoupe WIN32 MACOSX_BUNDLE ${BASE_SRC} ${SDL_SRC})

install(TARGETS simcoupe DESTINATION bin)
