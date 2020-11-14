# This cmake is a modified version of https://github.com/forexample/package-example/blob/master/Foo/CMakeLists.txt
cmake_minimum_required(VERSION 3.14)

project(ann_char VERSION 1.1.2)

# Set Libraries default visibility to hidden
set(CMAKE_CXX_VISIBILITY_PRESET hidden)
set(CMAKE_VISIBILITY_INLINES_HIDDEN YES)

# Add CMake Modules folder
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

# Handle installation of pdb-files for MSVC
include(HandlePDB)
###############################      Option      #############################################
option(BUILD_TEST "Build project test" OFF)
option(BUILD_EXAMPLES "Build Examples" OFF)
###############################      Option      #############################################

###############################      ANN      #############################################
add_library(ann_char)

add_library(ANN::ann_char ALIAS ann_char)

target_sources(ann_char
               PRIVATE
               src/ANN.cpp
               src/bd_pr_search.cpp
               src/bd_tree.cpp
               src/kd_dump.cpp
               src/kd_pr_search.cpp
               src/kd_split.cpp
               src/kd_util.cpp
               src/bd_fix_rad_search.cpp
               src/bd_search.cpp
               src/brute.cpp
               src/kd_fix_rad_search.cpp
               src/kd_search.cpp
               src/kd_tree.cpp
               src/perf.cpp
)

include(GenerateExportHeader)
# Generate:
#   * ${CMAKE_CURRENT_BINARY_DIR}/generated_headers/ANN/ANN_export.h with DLL_API
set(generated_headers "${CMAKE_CURRENT_BINARY_DIR}/generated_headers")
set(ann_export "${generated_headers}/ANN/ANN_export.h")
generate_export_header(ann_char
                       EXPORT_FILE_NAME ${ann_export}
                       EXPORT_MACRO_NAME  DLL_API)

target_include_directories(ann_char
                           PUBLIC
                           $<BUILD_INTERFACE:${generated_headers}>
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

target_compile_features(ann_char PUBLIC cxx_std_17)

include(CompilerWarnings)
set_project_warnings(ann_char)
###############################      ANN      #############################################

add_subdirectory(test)
add_subdirectory(sample)
add_subdirectory(ann2fig)

###############################      Install      #############################################
include(GNUInstallDirs)

set(config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

# Configuration
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(namespace "ANN")

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    "${PROJECT_NAME}Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    TARGETS ann_char
    EXPORT "${TARGETS_EXPORT_NAME}"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
    INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

# Headers:
install (DIRECTORY include/ DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")
# Export headers:
install (DIRECTORY "${generated_headers}/" DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

# install pdb-files
install(
      # this path is generated from HandlePDB.cmake
      DIRECTORY "${pdb_output_dir}/"
      DESTINATION ${pdb_dst}
)

install(
    EXPORT "${TARGETS_EXPORT_NAME}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)
###############################      Install      #############################################
