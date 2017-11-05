cmake_minimum_required(VERSION 2.8.3)
project(od_compressed_image_transport)

find_package(OpenCV REQUIRED)
find_package(catkin REQUIRED COMPONENTS cv_bridge dynamic_reconfigure image_transport)

# generate the dynamic_reconfigure config file
generate_dynamic_reconfigure_options(cfg/compressed_publisher.cfg cfg/compressed_subscriber.cfg)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS cv_bridge dynamic_reconfigure image_transport
  DEPENDS OpenCV
)

include_directories(include ${catkin_INCLUDE_DIRS} ${OpenCV_INCLUDE_DIRS})

file(GLOB od_SRC
    "cfg/*"
    "launch/*"
    "nodelet_plugins.xml"
    "src/basler_tof_bridge.cpp"
)

# Compressed Publisher
add_library(compressed_publisher "src/compressed_publisher.cpp" ${od_SRC})
target_link_libraries(
  compressed_publisher
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
)

# Compressed Subscriber
add_library(compressed_subscriber "src/compressed_subscriber.cpp" ${od_SRC})
target_link_libraries(
  compressed_subscriber
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
)

# Republisher
add_library(republisher "src/republisher.cpp" ${od_SRC})
target_link_libraries(
  republisher
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
)
