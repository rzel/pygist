cmake_minimum_required(VERSION 2.8)
project(compute_gist)
set(CMAKE_BUILD_TYPE "Release")

message("Searching in CMAKE_SOURCE_DIR: ${CMAKE_SOURCE_DIR}")


SET(FFTW_SEARCH_PATHS
    ${CMAKE_SOURCE_DIR}/fftw
    ${CMAKE_SOURCE_DIR}/fftw-3.2.2/
    ${CMAKE_SOURCE_DIR}/fftw-3.2.2/lib/
    ${CMAKE_SOURCE_DIR}/../fftw-3.2.2/
    ${CMAKE_SOURCE_DIR}/../fftw-3.2.2/lib/
    ${CMAKE_SOURCE_DIR}/build/dist/lib
    ${CMAKE_SOURCE_DIR}/../build/dist/lib
    $ENV{LD_LIBRARY_PATH} $ENV{FFTW_HOME}/lib )


#FIND_LIBRARY(FFTW_LIB NAMES fftw3 fftw3f fftw3f-3 fftw3l-3 PATHS ${FFTW_SEARCH_PATHS})
FIND_LIBRARY(FFTW_LIB NAMES fftw3f-3 PATHS ${FFTW_SEARCH_PATHS})

SET(FFTW_FOUND FALSE)
IF (FFTW_LIB)
    SET(FFTW_FOUND TRUE)
    MARK_AS_ADVANCED(FFTW_LIB)
ENDIF (FFTW_LIB)

IF (FFTW_FOUND)
    #IF (NOT FFTW_LIB_FIND_QUIETLY)
    MESSAGE(STATUS "Found FFTW: ${FFTW_LIB}")
    GET_FILENAME_COMPONENT(FFTW_PATH ${FFTW_LIB} PATH CACHE)
    SET(FFTW_INCLUDE_DIR ${FFTW_PATH}/../include CACHE FILEPATH "FFTW include directory.")
    MARK_AS_ADVANCED(FFTW_PATH)
    MARK_AS_ADVANCED(FFTW_INCLUDE_DIR)
    #ENDIF (NOT FFTW_LIB_FIND_QUIETLY)
    set(FFTW_LIBRARIES "${FFTW_LIBRARY}")
ELSE(FFTW_FOUND)
    #IF (FFTW_LIB_FIND_REQUIRED)
    MESSAGE(FATAL_ERROR "Could not find FFTW")
    #ENDIF (FFTW_LIB_FIND_REQUIRED)
ENDIF (FFTW_FOUND)



################
# OPEN MP
message(STATUS "Checking OpenMP")
find_package(OpenMP REQUIRED)
IF(OPENMP_FOUND)
    message("Found OpenMP! ^_^")
    # add flags for OpenMP
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} ${OpenMP_SHARED_LINKER_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
ELSE()
    message("Missed OpenMP! x_x")
ENDIF()
################

set(SOURCE_FILES 
    gist/gist.c 
    gist/gist.h
    gist/compute_gist.c 
    gist/standalone_image.c
    gist/standalone_image.h)

include_directories(${FFTW_PATH})
include_directories(gist)

add_executable(compute_gist ${SOURCE_FILES})

#SET_TARGET_PROPERTIES(gist PROPERTIES COMPILE_FLAGS "-DUSE_GIST -DSTANDALONE_GIST")
#SET_TARGET_PROPERTIES(compute_gist PROPERTIES COMPILE_FLAGS "-DUSE_GIST -DSTANDALONE_GIST")

########################################
#message("FFTW_LIB  = ${FFTW_LIB}")
#message("FFTW_PATH = ${FFTW_PATH}")
#target_link_libraries(gist -DUSE_GIST -DSTANDALONE_GIST)
target_link_libraries(compute_gist ${FFTW_LIB})