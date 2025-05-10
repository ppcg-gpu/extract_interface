cmake_minimum_required(VERSION 3.15)

project(extract_interface LANGUAGES C CXX)

option(EXTRACT_INTERFACE_FORCE_LLVM_VERSION "Force a specific LLVM version to be used (e.g. 15)" "")

# Add custom modules path
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" "${CMAKE_CURRENT_SOURCE_DIR}/ThirdParty/clang_feature_tests/cmake")

# Include CMake modules
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

# Find dependencies using our custom modules
# Pass the forced LLVM version to the FindLLVMClang module if specified
if(EXTRACT_INTERFACE_FORCE_LLVM_VERSION)
  set(LLVM_VERSION ${EXTRACT_INTERFACE_FORCE_LLVM_VERSION})
  message(STATUS "Enforcing LLVM version: ${EXTRACT_INTERFACE_FORCE_LLVM_VERSION}")
endif()

# Include the clang_feature_tests submodule
add_subdirectory(ThirdParty/clang_feature_tests)

# Set package information for config.h
set(PACKAGE "extract_interface")
set(PACKAGE_BUGREPORT "isl-development@googlegroups.com")
set(PACKAGE_NAME "extract_interface")
set(PACKAGE_STRING "extract_interface ${PROJECT_VERSION}")
set(PACKAGE_TARNAME "extract_interface")
set(PACKAGE_URL "")
set(PACKAGE_VERSION "${PROJECT_VERSION}")
set(VERSION "${PROJECT_VERSION}")

# Configure config.h from template
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/cmake/config.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/config.h
  @ONLY
)

# Define sources for the executable
set(EXTRACT_INTERFACE_SOURCES
  generator.h
  generator.cc
  python.h
  python.cc
  cpp.h
  cpp.cc
  cpp_conversion.h
  cpp_conversion.cc
  plain_cpp.h
  plain_cpp.cc
  set_lang_defaults_arg4.h
  template_cpp.h
  template_cpp.cc
  extract_interface.h
  extract_interface.cc
)

# Create executable (not a library)
add_executable(extract_interface ${EXTRACT_INTERFACE_SOURCES})

# Set include directories for the executable
# (includes build and source dirs, and LLVM/Clang includes)
target_include_directories(extract_interface
  PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_BINARY_DIR}/include
    ${LLVM_INCLUDE_DIRS}
    ${CLANG_INCLUDE_DIRS}
)

# Link Clang/LLVM libraries
target_link_libraries(extract_interface PUBLIC clang_feature_tests)

# Installation for executable
install(TARGETS extract_interface
  EXPORT EXTRACT_INTERFACETargets
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

