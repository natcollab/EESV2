#[===============================================================================[.rst
# EESV2/CMakeLists.txt
#
# EESV2 library.
# ^^^^^^^^^^^^
#
# Imported targets in global scope:
#
#   EESV2::Trader
#   EESV2::Quote
#
#]===============================================================================]

# Setup minimum required version of cmake, policy
# This command should be the first command in the project
cmake_minimum_required(VERSION 3.13...3.16)

# Setup Project name and version
# CMakeTest_VERSION and PROJECT_VERSION variables are set
project(EESV2 VERSION 1.0
  DESCRIPTION "Interface to EESV2 library"
  LANGUAGES CXX
  )

#=================================================================================

# Target: Trader
add_library(EESV2.Trader INTERFACE)
add_library(EESV2::Trader ALIAS EESV2.Trader)

set(EESV2_INCLUDE_INSTALL_DIR include/EESV2)
set(EESV2_LIB_INSTALL_DIR lib/EESV2)
set(EESV2_CMAKE_INSTALL_DIR lib/cmake/EESV2)

target_include_directories(EESV2.Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${EESV2_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(EESV2.Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESTraderApi.so>
             $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${EESV2_LIB_INSTALL_DIR}/libEESTraderApi.so>
  )

# Target: Quote
add_library(EESV2.Quote INTERFACE)
add_library(EESV2::Quote ALIAS EESV2.Quote)

target_include_directories(EESV2.Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${EESV2_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(EESV2.Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESQuoteApi.so>
             $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${EESV2_LIB_INSTALL_DIR}/libEESQuoteApi.so>
  )

#=================================================================================

# Install : Trader
install(DIRECTORY include/
  DESTINATION ${EESV2_INCLUDE_INSTALL_DIR}
  FILES_MATCHING PATTERN "EesTrader*.h"
  )

install(FILES lib/libEESTraderApi.so
  DESTINATION ${EESV2_LIB_INSTALL_DIR}
  )

# Install : Quote
install(DIRECTORY include/
  DESTINATION ${EESV2_INCLUDE_INSTALL_DIR}
  FILES_MATCHING PATTERN "EESQuote*.h"
  )

install(FILES lib/libEESQuoteApi.so
  DESTINATION ${EESV2_LIB_INSTALL_DIR}
  )

#=================================================================================

# Install export targets

install(TARGETS EESV2.Trader EXPORT EESV2_export)
set_target_properties(EESV2.Trader PROPERTIES EXPORT_NAME Trader)

install(TARGETS EESV2.Quote EXPORT EESV2_export)
set_target_properties(EESV2.Quote PROPERTIES EXPORT_NAME Quote)

# TODO: library path in export target file is relative path, which is not correct
install(EXPORT EESV2_export
  NAMESPACE EESV2::
  FILE EESV2Targets.cmake
  DESTINATION ${EESV2_CMAKE_INSTALL_DIR}
  )

#=================================================================================

# Install package config and version files

include(CMakePackageConfigHelpers)

# Package version file
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/EESV2ConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
  )

# Package config file
configure_package_config_file(
  ${CMAKE_CURRENT_LIST_DIR}/cmake/EESV2Config.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/EESV2Config.cmake
  INSTALL_DESTINATION ${EESV2_CMAKE_INSTALL_DIR}
  PATH_VARS
    EESV2_INCLUDE_INSTALL_DIR
    EESV2_LIB_INSTALL_DIR
  )

# Install the config and version files
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/EESV2Config.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/EESV2ConfigVersion.cmake
  DESTINATION ${EESV2_CMAKE_INSTALL_DIR}
  )
