cmake_minimum_required(VERSION 2.8)
set (CMAKE_CXX_STANDARD 14)
project(linemodLevelup_pybind)

SET(CMAKE_BUILD_TYPE "Release")
#SET(CMAKE_BUILD_TYPE "Debug")
SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g2 -ggdb")
SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")

find_package(OpenCV 3 REQUIRED)

# open3d
find_package(Open3D REQUIRED)
if (Open3D_FOUND)
    message(STATUS "Found Open3D ${Open3D_VERSION}")

    include_directories(${Open3D_INCLUDE_DIRS})
    link_directories(${Open3D_LIBRARY_DIRS})

    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${Open3D_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${Open3D_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${Open3D_EXE_LINKER_FLAGS}")
else ()
    message(SEND_ERROR "Open3D not found")
endif ()

find_package(PythonLibs 3 REQUIRED)
find_package(pybind11 REQUIRED)

include(Cmake/FindNumPy.cmake)

include_directories(${OpenCV_INCLUDE_DIRS} ${PYTHON_INCLUDE_DIRS}
    ${PYTHON_NUMPY_INCLUDE_DIR})

set(SOURCE_PYBIND
    pybind11.cpp
    np2mat/ndarray_converter.cpp)

set(SOURCE
    linemodLevelup.cpp)

pybind11_add_module(${PROJECT_NAME} ${SOURCE} ${SOURCE_PYBIND})
target_link_libraries(${PROJECT_NAME} PRIVATE ${OpenCV_LIBS} ${Open3D_LIBRARIES})

add_executable(${PROJECT_NAME}_executable ${SOURCE} test.cpp)
target_link_libraries(${PROJECT_NAME}_executable ${OpenCV_LIBS} ${Open3D_LIBRARIES})

