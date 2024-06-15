cmake_minimum_required(VERSION 3.18)

# Parse parmetis.h to determine the version 
set(ParMETIS_INCLUDE_FILE ${CMAKE_CURRENT_LIST_DIR}/include/parmetis.h)
if(EXISTS "${ParMETIS_INCLUDE_FILE}")
  file(STRINGS "${ParMETIS_INCLUDE_FILE}" include_lines)
  foreach(line ${include_lines})
    if(line MATCHES "^#define (PARMETIS_MAJOR_VERSION|PARMETIS_MINOR_VERSION|PARMETIS_SUBMINOR_VERSION)[ \t]*([0-9]+)$")
      set(${CMAKE_MATCH_1} ${CMAKE_MATCH_2})
    endif()
  endforeach()
else()
  set(PARMETIS_MAJOR_VERSION 4)
  set(PARMETIS_MINOR_VERSION 0)
  set(PARMETIS_SUBMINOR_VERSION 3)
endif()

set(ParMETIS_VERSION "${PARMETIS_MAJOR_VERSION}.${PARMETIS_MINOR_VERSION}.${PARMETIS_SUBMINOR_VERSION}")

project(ParMETIS
  VERSION ${ParMETIS_VERSION}
  LANGUAGES C)

include(GNUInstallDirs)

get_filename_component(abs "${CMAKE_CURRENT_LIST_DIR}" ABSOLUTE)
set(ParMETIS_PATH "${abs}")
unset(abs)
set(LIBParMETIS_PATH "${METIS_PATH}/libparmetis")

# Search for MPI.
find_package(MPI REQUIRED)

# if(NOT MPI_FOUND)
#   message(FATAL_ERROR "mpi is not found")
# endif()
# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${MPI_COMPILE_FLAGS}")

# Get METIS
find_package(METIS REQUIRED)

# Prepare libraries.
if(SHARED)
  set(ParMETIS_LIBRARY_TYPE SHARED)
else()
  set(ParMETIS_LIBRARY_TYPE STATIC)
endif()

include(./conf/gkbuild.cmake)

# List of paths that the compiler will search for header files.
# i.e., the -I equivalent
#include_directories(include)
#include_directories(${MPI_INCLUDE_PATH})
#include_directories(${GKLIB_PATH}/include)
#include_directories(${METIS_PATH}/include)
#include_directories(${CMAKE_INSTALL_PREFIX}/include)

# List of paths that the compiler will search for library files.
# i.e., the -L equivalent
#link_directories(${GKLIB_PATH}/lib)
#link_directories(${METIS_PATH}/lib)
#link_directories(${CMAKE_INSTALL_PREFIX}/lib)

# List of directories that cmake will look for CMakeLists.txt
#add_subdirectory(include)
add_subdirectory(libparmetis)
add_subdirectory(programs)

# This is for testing during development and is not being distributed
#add_subdirectory(test)
