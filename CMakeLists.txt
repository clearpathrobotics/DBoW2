cmake_minimum_required(VERSION 3.0)
project(DBoW2 CXX C)

option(BUILD_DBoW2 "Build DBoW2" ON)
option(BUILD_UTIL "Build utility executables" ON)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(CMAKE_BUILD_TYPE Release)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

if(MSVC)
  add_compile_options(/W4)
else()
  add_compile_options(-Wall -Wextra -Wpedantic -std=c++11)
endif()

set(SOURCES
  src/BowVector.cpp
  src/FeatureVector.cpp
  src/QueryResults.cpp
  src/ScoringObject.cpp
  src/FORB.cpp
  src/FBrief.cpp)

find_package(OpenCV 3.0 REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

include_directories(include)

if(BUILD_DBoW2)
  set(LIB_TYPE "SHARED")
  if(WIN32)
    set(LIB_TYPE "STATIC")
  endif(WIN32)
  add_library(${PROJECT_NAME} ${LIB_TYPE} ${SOURCES})
  target_include_directories(${PROJECT_NAME} PUBLIC include/DBoW2/ include/)
  target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
  set_target_properties(${PROJECT_NAME} PROPERTIES CXX_STANDARD 11)
endif(BUILD_DBoW2)

configure_file(DBoW2.cmake.in ${PROJECT_BINARY_DIR}/DBoW2Config.cmake @ONLY)

if(BUILD_DBoW2)
  install(DIRECTORY include/DBoW2
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  install(TARGETS ${PROJECT_NAME}
    DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
endif(BUILD_DBoW2)

if(BUILD_UTIL)
  add_executable(convert_orb_vocab util/convert_orb_vocab.cpp)
  target_link_libraries(convert_orb_vocab ${PROJECT_NAME})
endif(BUILD_UTIL)

install(FILES ${PROJECT_BINARY_DIR}/DBoW2Config.cmake
  DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/DBoW2/)
