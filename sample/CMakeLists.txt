cmake_minimum_required(VERSION 3.14)
project(ann_char_sample)

include(HandlePDB)

add_executable(${PROJECT_NAME})

target_sources(${PROJECT_NAME} 
               PRIVATE 
               ann_sample.cpp)

target_link_libraries(${PROJECT_NAME} PRIVATE ANN::ann_char)

target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_17)

include(CompilerWarnings)
set_project_warnings(${PROJECT_NAME})