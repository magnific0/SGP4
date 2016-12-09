#    Copyright (c) 2010-2016, Delft University of Technology
#    All rigths reserved
#
#    This file is part of the Tudat. Redistribution and use in source and
#    binary forms, with or without modification, are permitted exclusively
#    under the terms of the Modified BSD license. You should have received
#    a copy of the license with this file. If not, please or visit:
#    http://tudat.tudelft.nl/LICENSE.
#

# Specify minimum CMake version.
cmake_minimum_required( VERSION 2.6 )

# Specific project name.
project( SGP4 )

# Detect if SGP4 is build standalone or from within project
if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
  message(STATUS "Building ${PROJECT_NAME} standalone.")
  include("${CMAKE_CURRENT_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
else()
  message(STATUS "Building ${PROJECT_NAME} from within ${CMAKE_PROJECT_NAME}.")
  include("${CMAKE_CURRENT_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
  include("${CMAKE_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
  set(PROJECTROOT ${PROJECTROOT} PARENT_SCOPE)
endif()

# Define the source directory and set custom bin and lib directories for building.
set(SRCROOT ${PROJECT_SOURCE_DIR})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${SRCROOT}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${SRCROOT}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${SRCROOT}/bin)

# Apply fixes for safe versions of strcpy etc., which are MSVC only
if(NOT MSVC)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Dstrcpy_s=strcpy -Dscanf_s=scanf")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Dfscanf_s=fscanf -Dsscanf_s=sscanf")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Dstrncpy_s=strncpy -Dsprintf_s=sprintf")
endif()

set(CMAKE_BUILD_TYPE Release)

include_directories("${SRCROOT}/astmath/astMath")
include_directories("${SRCROOT}/asttime/astTime")
include_directories("${SRCROOT}/ast2body/ast2Body")
include_directories("${SRCROOT}/astiod/astIOD")
include_directories("${SRCROOT}/coordfk5/coordFK5")
include_directories("${SRCROOT}/eopspw/EopSpw")
include_directories("${SRCROOT}/SGP4/SGP4")
include_directories("${SRCROOT}/DTM_12/DTM_12")
include_directories("${SRCROOT}/MSIS_Vers/MSIS_Vers")
include_directories("${SRCROOT}/SGP4DC/SGP4DC")


# Set headers and sources for the different libraries
set(ASTLIB_HEADERS "${SRCROOT}/astmath/astMath/astMath.h"
                   "${SRCROOT}/asttime/astTime/astTime.h"
                   "${SRCROOT}/ast2body/ast2Body/ast2Body.h"
                   "${SRCROOT}/astiod/astIOD/astIOD.h"
                   "${SRCROOT}/coordfk5/coordFK5/coordFK5.h"
                   "${SRCROOT}/eopspw/EopSpw/EopSpw.h")
set(ASTLIB_SOURCES "${SRCROOT}/astmath/astMath/astMath.cpp"
                   "${SRCROOT}/asttime/astTime/astTime.cpp"
                   "${SRCROOT}/ast2body/ast2Body/ast2Body.cpp"
                   "${SRCROOT}/astiod/astIOD/astIOD.cpp"
                   "${SRCROOT}/coordfk5/coordFK5/coordFK5.cpp"
                   "${SRCROOT}/eopspw/EopSpw/EopSpw.cpp")

set(SGP4_HEADERS "${SRCROOT}/SGP4/SGP4/SGP4.h")
set(SGP4_SOURCES "${SRCROOT}/SGP4/SGP4/SGP4.cpp")

set(SGP4DC_HEADERS "${SRCROOT}/SGP4DC/SGP4DC/SGP4DC.h"
                   ${SGP4_HEADERS}
                   ${ASTLIB_HEADERS})
set(SGP4DC_SOURCES "${SRCROOT}/SGP4DC/SGP4DC/SGP4DC.cpp"
                   ${SGP4_SOURCES}
                   ${ASTLIB_SOURCES})

set(DTM_12_HEADERS "${SRCROOT}/DTM_12/DTM_12/DTM_12.h")
set(DTM_12_SOURCES "${SRCROOT}/DTM_12/DTM_12/DTM_12.cpp")

set(MSISXX_HEADERS "${SRCROOT}/MSIS_Vers/MSIS_Vers/MSIS_Vers.h")
set(MSISXX_SOURCES "${SRCROOT}/MSIS_Vers/MSIS_Vers/MSIS_Vers.cpp")


# Add static libraries.
add_library(ASTLIB STATIC ${ASTLIB_SOURCES} ${ASTLIB_HEADERS})
add_library(SGP4   STATIC ${SGP4_SOURCES}   ${SGP4_HEADERS})
add_library(SGP4DC STATIC ${SGP4DC_SOURCES} ${SGP4DC_HEADERS})
add_library(DTM_12 STATIC ${DTM_12_SOURCES} ${DTM_12_HEADERS})
add_library(MSISXX STATIC ${MSISXX_SOURCES} ${MSISXX_HEADERS})

# Define option and build test executables
option(SGP4_WITH_TESTS "Compile SGP4 test executables" ON)
if(SGP4_WITH_TESTS)
        enable_testing()

  add_executable(TestSGP4 "${SRCROOT}/TestSGP4/TestSGP4/TestSGP4.cpp")
  target_link_libraries(TestSGP4 SGP4)
  add_test(TestSGP4 "${SRCROOT}/bin/TestSGP4")

  #add_executable(TestSGP4DC "${SRCROOT}/TestSGP4DC.cpp")
  #target_link_libraries(TestSGP4DC ASTLIB SGP4 SGP4DC)
  #add_test(TestSGP4DC "${SRCROOT}/bin/testSGP4DC")

  add_executable(TestDTM "${SRCROOT}/TestDTM/TestDTM/TestDTM.cpp")
  target_link_libraries(TestDTM ASTLIB DTM_12)
  add_test(TestDTM "${SRCROOT}/bin/TestDTM")

  add_executable(TestMSIS "${SRCROOT}/TestMSIS/TestMSIS/TestMSIS.cpp")
  target_link_libraries(TestMSIS MSISXX)
  add_test(TestMSIS "${SRCROOT}/bin/TestMSIS")

endif()

# End of file.
