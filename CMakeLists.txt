cmake_minimum_required(VERSION 3.10)
set (CMAKE_INTERPROCEDURAL_OPTIMIZATION OFF)

project(PhysicalSimulation)
set (CMAKE_CXX_STANDARD 14)
set (CMAKE_POSITION_INDEPENDENT_CODE ON)
set (CMAKE_BUILD_RPATH_USE_ORIGIN TRUE)

option(USE_ADDRESS_SANITIZER "Enable address sanitiezr when CMAKE_BUILD_TYPE is Debug" OFF)
option(USE_ADDRESS_SANITIZER_RELEASE "Enable address sanitiezr in Release mode" OFF)

if (USE_ADDRESS_SANITIZER)
	set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=address")
	set (CMAKE_LINKER_FLAGS_DEBUG "${CMAKE_LINKER_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=address")
endif()

if (USE_ADDRESS_SANITIZER_RELEASE)
	set (CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -fno-omit-frame-pointer -fsanitize=address")
	set (CMAKE_LINKER_FLAGS_RELEASE "${CMAKE_LINKER_FLAGS_RELEASE} -fno-omit-frame-pointer -fsanitize=address")
	set (CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} -fno-omit-frame-pointer -fsanitize=address")
	set (CMAKE_LINKER_FLAGS_RELWITHDEBINFO "${CMAKE_LINKER_FLAGS_RELWITHDEBINFO} -fno-omit-frame-pointer -fsanitize=address")
endif()

include(ExternalProject)
set(EXTERNAL_PROJECTS_PREFIX ${CMAKE_BINARY_DIR}/external)
set(EXTERNAL_PROJECTS_INSTALL_PREFIX ${CMAKE_BINARY_DIR}/installed)

option(PSIM_ENABLE_VISUALIZER "Build the visualizer" ON)

# set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

# Common functions
include(cmake/common_functions.cmake)

# Core libraries

# Threads
find_package(Threads REQUIRED)

# Eigen 3
find_package(Eigen3 REQUIRED NO_MODULE)

add_library(psim_std_dep INTERFACE)
target_link_libraries(psim_std_dep INTERFACE Eigen3::Eigen Threads::Threads)

add_subdirectory(third-party/tetgen/)
add_subdirectory(lib)

if (PSIM_ENABLE_VISUALIZER)
	include(cmake/igl_viewer.cmake)
	# add_subdirectory(sln)
endif()

add_subdirectory(src)
