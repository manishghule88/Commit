# Part of build system for empty_app
#
# (C)2024 Zeus Numerix Pvt. Ltd.

# Specify the name of the current binary
set(APP_NAME empty_app)

## build option flags ##########################################################
#

#
# Find includes in corresponding build directories
#set(CMAKE_INCLUDE_CURRENT_DIR OFF)

## custom cmake modules ########################################################


## custom settings #############################################################
#file(TO_CMAKE_PATH "$ENV{EXTRA_INCLUDE_PATHS}" EXTRA_INCLUDE_PATHS)
#file(TO_CMAKE_PATH "$ENV{EXTRA_LIB_PATHS}" EXTRA_LIB_PATHS)
#file(TO_CMAKE_PATH "$ENV{EXTRA_CMAKE_MODULE_PATHS}" EXTRA_CMAKE_MODULE_PATHS)
#list(APPEND CMAKE_MODULE_PATH ${EXTRA_CMAKE_MODULE_PATHS})

if (NOT CMAKE_BUILD_TYPE)
    message(STATUS "No build type selected, default to RelWithDebInfo")
    set(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING "Build type." FORCE)
endif()

## project dependencies ########################################################

## other settings
 
#
# custom includes
include_directories(
    SYSTEM
    ${EXTRA_INCLUDE_PATHS}
)

# custom link folders
link_directories(
    ${EXTRA_LIB_PATHS}
)

#
# Extract working copy information for versioning
execute_process(
  COMMAND git rev-parse --abbrev-ref HEAD
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_BRANCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

execute_process(
  COMMAND git log -1 --format=%h
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_COMMIT_HASH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
add_definitions( -DZN_SVNREVISION="${GIT_COMMIT_HASH}")
message(STATUS "Building from commit: ${GIT_COMMIT_HASH} @${GIT_BRANCH}")

# the build time macro
string(TIMESTAMP BUILDTIME "%Y%m%dT%H%M")
add_definitions( -DZN_BUILDTIME="${BUILDTIME}")

#
# pre-processor definitions

#
# license definitions
# IMPORTANT: Add this macro when software is deployed.
# add_definitions( -DZN_LICENSE_ENABLED )

# enable cppcheck static analysis
#set( CMAKE_CXX_CPPCHECK cppcheck )

## Setup the Source Files
set( PRJ_SRC
    main.cpp conversion.cpp conversion.h function.h
)

## Setup the Executable
add_executable( ${APP_NAME} WIN32 ${PRJ_SRC} )

## Custom commands for copying compiled binaries to the run folder.
#add_custom_command(TARGET ${APP_NAME}
#                   POST_BUILD
#                   COMMAND ${CMAKE_COMMAND} -E make_directory ${empty_app_SOURCE_DIR}/run/bin
#                   COMMENT "Create ${empty_app_SOURCE_DIR}/run/bin folder.")
                   
#add_custom_command(TARGET ${APP_NAME}
#                   POST_BUILD
#                   COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:${APP_NAME}> ${empty_app_SOURCE_DIR}/run/bin
#                   COMMENT "Copying ${APP_NAME} to run/bin/ folder.")
                   
#
# compiler options

target_compile_options( ${APP_NAME} PUBLIC -std=c++17 -Wall -Wextra 
        -Wduplicated-cond -Wnull-dereference -Wold-style-cast -Wuseless-cast 
        -Wshadow -Wpedantic -Wno-deprecated-declarations )

target_link_libraries(
    ${APP_NAME}
  #  snmpapi
  #  ws2_32
)

