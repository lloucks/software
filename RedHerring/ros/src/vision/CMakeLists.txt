##############################################################################
# CMake                                                                      #
##############################################################################
cmake_minimum_required(VERSION 2.8.3)
project(vision)
set(CMAKE_CXX_FLAGS "-std=c++11")

##############################################################################
# Catkin                                                                     #
##############################################################################

find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  image_transport
  roscpp
  sensor_msgs
  std_msgs
  message_generation
)

add_service_files(
  FILES
  webcam.srv
  enable_detection.srv
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

# This is useful when we want other packages to be able to use this package
# E.g. if another package wanted to include a few headers from here
# To run in their own nodes, they would include this package "vision"
# And we would uncomment INCLUDE_DIRS, but generally everything happens here
# SO theres no need (for now)
catkin_package(
#  INCLUDE_DIRS include
  LIBRARIES vision
  CATKIN_DEPENDS cv_bridge image_transport roscpp sensor_msgs message_runtime std_msgs
#  DEPENDS system_lib
)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

##############################################################################
# Binaries                                                                   #
##############################################################################

# Required packages
find_package(OpenCV 2 REQUIRED)

# Separate nodes in the package
add_executable(sender src/sender.cpp src/webcam.cpp include/video_api.h)
add_dependencies(sender ${vision_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(sender ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})

add_executable(reciever src/reciever.cpp)
add_dependencies(reciever ${vision_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(reciever ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})

add_executable(colour_detection src/colour_detection.cpp)
add_dependencies(colour_detection ${vision_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(colour_detection ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})
