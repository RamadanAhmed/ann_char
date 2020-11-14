cmake_minimum_required(VERSION 3.14)
project(ann_char_test)

include(HandlePDB)

add_executable(${PROJECT_NAME})

target_sources(${PROJECT_NAME} 
               PRIVATE 
               ann_test.cpp
               rand.cpp)

target_link_libraries(${PROJECT_NAME} PRIVATE ANN::ann_char)

target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_17)

target_compile_definitions(${PROJECT_NAME}
                           PRIVATE
                           $<$<CXX_COMPILER_ID:MSVC>:ANN_NO_RANDOM>)

include(CompilerWarnings)
set_project_warnings(${PROJECT_NAME})