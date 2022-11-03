cmake_minimum_required(VERSION 2.8.3)
project(mlcc)

set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++17")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
)

find_package(PCL REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(Ceres REQUIRED)

catkin_package(
  CATKIN_DEPENDS roscpp rospy std_msgs message_runtime
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
  ${CERES_INCLUDE_DIRS}
)

catkin_package(
  CATKIN_DEPENDS geometry_msgs nav_msgs roscpp rospy std_msgs
  DEPENDS EIGEN3 PCL OpenCV
  INCLUDE_DIRS
)

add_executable(pose_refine source/pose_refine.cpp)
target_link_libraries(pose_refine ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})

add_executable(extrinsic_refine source/extrinsic_refine.cpp)
target_link_libraries(extrinsic_refine ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})

add_executable(global source/global.cpp)
target_link_libraries(global ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})

add_executable(calib_camera source/camera_calib.cpp)
target_link_libraries(calib_camera ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${CERES_LIBRARIES} ${OpenCV_LIBS})

install(TARGETS pose_refine extrinsic_refine global calib_camera
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY config/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/config
  FILES_MATCHING PATTERN "*"
)

install(DIRECTORY rviz_cfg/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/rviz_cfg
  FILES_MATCHING PATTERN "*.rviz"
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  FILES_MATCHING PATTERN "*.launch"
)
