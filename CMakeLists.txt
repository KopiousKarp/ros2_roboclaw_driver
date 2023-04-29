cmake_minimum_required(VERSION 3.5)
project(ros2_roboclaw_driver)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

add_compile_options(-g)

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs)
find_package(rclcpp REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)

set(msg_files
  "msg/RoboClawStatus.msg"
)

set(srv_files
  "srv/ResetEncoders.srv"
)

rosidl_generate_interfaces(${PROJECT_NAME}
  ${msg_files}
  ${srv_files}
)

add_executable(
  ros2_roboclaw_driver_node 
  src/motor_driver_node.cpp
  src/motor_driver.cpp
  src/roboclaw.cpp
)

ament_target_dependencies(
  ros2_roboclaw_driver_node 
  rclcpp
  geometry_msgs
  nav_msgs
  sensor_msgs
  std_msgs
)

# NB! The CMake function rosidl_target_interfaces() is depricated in ROS2 Humble
#rosidl_target_interfaces(
#  ros2_roboclaw_driver_node
#  ${PROJECT_NAME}
#  "rosidl_typesupport_cpp"
#)

# Function rosidl_target_interfaces() is replaced by the code below:
# This will create a target called ros2_roboclaw_driver_node__rosidl_typesupport_cpp 
# using rosidl_get_typesupport_target, 
# and then link the ros2_roboclaw_driver_node target to it using target_link_libraries. 
# This will ensure that the appropriate ROS 2 message and service typesupport libraries 
# are linked with the ros2_roboclaw_driver_node target.
rosidl_get_typesupport_target(ros2_roboclaw_driver_node__rosidl_typesupport_cpp 
  ${PROJECT_NAME} "rosidl_typesupport_cpp"
)

target_link_libraries(ros2_roboclaw_driver_node 
  ${ros2_roboclaw_driver_node__rosidl_typesupport_cpp}
)
# -----

# Install config files.
install(DIRECTORY
  config
  DESTINATION share/${PROJECT_NAME}/
)

# Install launch files.
install(DIRECTORY
  launch
  DESTINATION share/${PROJECT_NAME}/
)

install(TARGETS
  ros2_roboclaw_driver_node
  DESTINATION lib/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_export_dependencies(rosidl_default_runtime)

ament_package()
