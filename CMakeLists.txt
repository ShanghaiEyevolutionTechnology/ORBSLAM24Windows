cmake_minimum_required(VERSION 2.8)
project(ORB_SLAM2)

IF(NOT CMAKE_BUILD_TYPE)
#  SET(CMAKE_BUILD_TYPE Release)
ENDIF()

#MESSAGE("Build type: " ${CMAKE_BUILD_TYPE})

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -W0 -MT -bigobj")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -W0 -MT -bigobj")


# Check C++11 or C++0x support
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
SET(DBUILD_SHARED true)

if(COMPILER_SUPPORTS_CXX11)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
   add_definitions(-DCOMPILEDWITHC11)
   message(STATUS "Using flag -std=c++11.")
elseif(COMPILER_SUPPORTS_CXX0X)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
   add_definitions(-DCOMPILEDWITHC0X)
   message(STATUS "Using flag -std=c++0x.")
#else()
#   message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)

find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(OpenGL REQUIRED)

include_directories(
${PROJECT_SOURCE_DIR}
${PROJECT_SOURCE_DIR}/include
${EIGEN3_INCLUDE_DIR}
${PROJECT_SOURCE_DIR}/Thirdparty/DBoW2
${PROJECT_SOURCE_DIR}/Thirdparty/g2o
${OPENGL_INCLUDE_DIR}
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/include
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/glew/include
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/libjpeg/include
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/libpng/include
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/zlib/include
)

add_library(${PROJECT_NAME}
src/System.cc
src/Tracking.cc
src/LocalMapping.cc
src/LoopClosing.cc
src/ORBextractor.cc
src/ORBmatcher.cc
src/FrameDrawer.cc
src/Converter.cc
src/MapPoint.cc
src/KeyFrame.cc
src/Map.cc
src/MapDrawer.cc
src/Optimizer.cc
src/PnPsolver.cc
src/Frame.cc
src/KeyFrameDatabase.cc
src/Sim3Solver.cc
src/Initializer.cc
src/Viewer.cc
)

target_link_libraries(
${PROJECT_NAME}
${OpenCV_LIBS}
${PROJECT_SOURCE_DIR}/Thirdparty/DBoW2/lib/DBoW2.lib
${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/g2o.lib
${OPENGL_LIBRARIES}
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/lib/pangolin.lib
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/glew/lib/glew.lib
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/libjpeg/lib/jpeg.lib
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/libpng/lib/libpng16_static.lib
${PROJECT_SOURCE_DIR}/Thirdparty/Pangolin/external/zlib/lib/zlibstatic.lib
)

set_target_properties(${PROJECT_NAME}
	PROPERTIES 
	ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib
	ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/lib
	ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/lib)

# Build examples

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/RGB-D)

add_executable(rgbd_tum
Examples/RGB-D/rgbd_tum.cc)
target_link_libraries(rgbd_tum ${PROJECT_NAME})

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/Stereo)

add_executable(stereo_kitti
Examples/Stereo/stereo_kitti.cc)
target_link_libraries(stereo_kitti ${PROJECT_NAME})

add_executable(stereo_euroc
Examples/Stereo/stereo_euroc.cc)
target_link_libraries(stereo_euroc ${PROJECT_NAME})

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/Monocular)

add_executable(mono_tum
Examples/Monocular/mono_tum.cc)
target_link_libraries(mono_tum ${PROJECT_NAME})

add_executable(mono_kitti
Examples/Monocular/mono_kitti.cc)
target_link_libraries(mono_kitti ${PROJECT_NAME})

add_executable(mono_euroc
Examples/Monocular/mono_euroc.cc)
target_link_libraries(mono_euroc ${PROJECT_NAME})


set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/leadsense)
add_subdirectory(leadsense)