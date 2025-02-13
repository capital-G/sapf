cmake_minimum_required(VERSION 3.25)
project(sapf)

set(CMAKE_CXX_STANDARD 17)

file(GLOB SAPF_SOURCES ${CMAKE_SOURCE_DIR}/src/*.cpp)
file(GLOB SAPF_OBJ_C_SOURCES ${CMAKE_SOURCE_DIR}/src/*.mm)
set_source_files_properties(${SAPF_OBJ_C_SOURCES} PROPERTIES COMPILE_FLAGS "-x objective-c++")
list(APPEND SAPF_SOURCES ${SAPF_OBJ_C_SOURCES})
add_executable(sapf ${SAPF_SOURCES})
target_include_directories(sapf PRIVATE ${CMAKE_SOURCE_DIR}/include)

# add mantalib
file(GLOB MANTA_SOURCES ${CMAKE_SOURCE_DIR}/libmanta/*.cpp)
add_library(libmanta STATIC ${MANTA_SOURCES})
# assuming hidapi is installed, e.g. via brew
find_package(hidapi REQUIRED)
target_link_libraries(libmanta PRIVATE hidapi::hidapi)
target_link_libraries(sapf libmanta)
target_include_directories(sapf PRIVATE ${CMAKE_SOURCE_DIR}/libmanta)

# link macOS frameworks
target_link_libraries(sapf "-framework AudioToolbox")
target_link_libraries(sapf "-framework CoreAudio")
target_link_libraries(sapf "-framework Foundation")
target_link_libraries(sapf "-framework CoreGraphics")
target_link_libraries(sapf "-framework CoreMIDI")
target_link_libraries(sapf "-framework Carbon")
target_link_libraries(sapf "-framework Accelerate")
target_link_libraries(sapf "-framework Cocoa")

# link libedit
IF(NOT LIBEDIT_PATH)
    set(LIBEDIT_PATH "/usr/local/opt/libedit/")
ENDIF()
target_link_libraries(sapf ${LIBEDIT_PATH}/lib/libedit.dylib)
target_include_directories(sapf PRIVATE ${LIBEDIT_PATH}/include)

set(CMAKE_GENERATOR "Xcode")
