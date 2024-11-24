cmake_minimum_required(VERSION 3.8 FATAL_ERROR)
project(cppflow LANGUAGES CXX)

include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}/cmake)

set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_SOURCE_DIR}/cmake/modules")

# Library target
find_package(tensorflow REQUIRED)
add_library(cppflow INTERFACE)

target_compile_features(cppflow INTERFACE cxx_std_17)
target_link_libraries(cppflow INTERFACE
  ${tensorflow_LIBRARIES}
)

# Build examples
option(BUILD_EXAMPLES "Build examples" ON)

if(BUILD_EXAMPLES)
  add_subdirectory(examples)
endif()

target_include_directories(cppflow INTERFACE
  ${tensorflow_INCLUDE_DIRS}
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

set_target_properties(cppflow
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY ${BINARY_DIR}/lib
  LIBRARY_OUTPUT_DIRECTORY ${BINARY_DIR}/lib
  RUNTIME_OUTPUT_DIRECTORY ${BINARY_DIR}
  VERSION ${PROJECT_VERSION}
  SOVERSION 1
  DEBUG_POSTFIX "d"
)

install(TARGETS cppflow EXPORT cppflowConfig
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include DESTINATION ${CMAKE_INSTALL_PREFIX})
install(EXPORT cppflowConfig DESTINATION share/cppflowConfig/cmake)
export(TARGETS cppflow FILE cppflowConfig.cmake)

# Install find modules
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/ DESTINATION ${INSTALL_CONFIGDIR}/modules)