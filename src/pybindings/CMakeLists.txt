cmake_minimum_required(VERSION 3.1)

if(NOT FAST_DOWNWARD_MAIN_CMAKELISTS_READ)
    message(
        FATAL_ERROR
        "Run cmake on the CMakeLists.txt in the 'src' directory, "
        "not the one in 'src/search'. Please delete CMakeCache.txt "
        "from the current directory and restart cmake.")
endif()

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

## == Project ==

# TODO: Does that need to be project(pydownward) or project(downward)?
project(pydownward)

# TODO: use multi-line strings to break up the long string when we switch to CMake 3.
# https://stackoverflow.com/questions/7637539/how-to-split-strings-across-multiple-lines-in-cmake
option(
  USE_GLIBCXX_DEBUG
  "Enable the libstdc++ debug mode that does additional safety checks. (On Linux systems, g++ and clang++ usually use libstdc++ for the C++ library.) The checks come at a significant performance cost and should only be enabled in debug mode. Enabling them makes the binary incompatible with libraries that are not compiled with this flag, which can lead to hard-to-debug errors."
  FALSE)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
 
# TODO: Can you use the same flags here as in search/CMakeLists.txt?
# If yes, uncomment following two and delete the subsequent two.
# fast_downward_set_compiler_flags()
# fast_downward_set_linker_flags()
set(CMAKE_CXX_FLAGS "-O3")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")

include_directories("${CMAKE_CURRENT_SOURCE_DIR}/../search/ext/")
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/../search/")

# TODO: Need to integrate DownwardFiles.cmake
file (GLOB SOURCE_FILES "../search/*.cc" "../search/*/*.cc")
file (GLOB HEADER_FILES "../search/*.h*" "../search/*/*.h*")
file (GLOB PYTHON_FILES "./*.cc" "./*.h")

# Set up such that XCode organizes the files
#source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SOURCE_FILES} ${HEADER_FILES} ${PYTHON_FILES} )

set(PYTHON_EXECUTABLE=/usr/bin/python3)
find_package(pybind11 REQUIRED)
pybind11_add_module(pydownward 
	${SOURCE_FILES}
	${HEADER_FILES}
	${PYTHON_FILES}
)

target_link_libraries(pydownward PUBLIC)

## == Includes ==

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../search/ext)

# TODO: Is the section "libraries" from search/CMakeLists.txt needed here to exactly the same extent?
## == Libraries ==

## == Install ==

set(PYTHON_LIBRARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../../trypybindings)
install(TARGETS pydownward
  COMPONENT python
  LIBRARY DESTINATION "${PYTHON_LIBRARY_DIR}"
  )