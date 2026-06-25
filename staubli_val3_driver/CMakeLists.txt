cmake_minimum_required(VERSION 3.0.2)
project(staubli_val3_driver)

set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(catkin REQUIRED COMPONENTS industrial_robot_client simple_message)

catkin_package(
  CATKIN_DEPENDS industrial_robot_client
)

include_directories(
  include
  include/include
  include/src
  ${catkin_INCLUDE_DIRS}
)

add_library(staubli_interfaces
    src/staubli_joint_trajectory_interface.cpp
    src/staubli_joint_trajectory_streamer.cpp
)

add_definitions(-DLINUXSOCKETS)

add_executable(${PROJECT_NAME}_motion_streaming_interface
  src/staubli_joint_streamer.cpp)

target_link_libraries(${PROJECT_NAME}_motion_streaming_interface 
  industrial_robot_client 
  simple_message
  staubli_interfaces
  ${catkin_LIBRARIES})

set_target_properties(${PROJECT_NAME}_motion_streaming_interface
  PROPERTIES OUTPUT_NAME motion_streaming_interface PREFIX "")
target_compile_definitions(
  ${PROJECT_NAME}_motion_streaming_interface
  PRIVATE
  ${industrial_robot_client_DEFINITIONS})

if (CATKIN_ENABLE_TESTING)
  find_package(roslaunch REQUIRED)
  roslaunch_add_file_check(tests/roslaunch_test.xml)
endif()

install(
  TARGETS
    ${PROJECT_NAME}_motion_streaming_interface
  DESTINATION
    ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(
  TARGETS
    staubli_interfaces
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY launch val3
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(FILES README.md DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
