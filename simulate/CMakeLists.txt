cmake_minimum_required(VERSION 3.16)
project(unitree_mujoco)

if (CMAKE_BUILD_TYPE MATCHES "Debug")
  message("Debug mode")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17 -g -O0 -fPIC")
else()
  message("Release mode")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17 -O3 -DNDEBUG -fPIC")
endif()

# list(APPEND CMAKE_PREFIX_PATH "/opt/unitree_robotics/lib/cmake")

find_package(unitree_sdk2 REQUIRED)
find_package(Boost REQUIRED COMPONENTS program_options)

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rcl_interfaces REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(OpenMP REQUIRED)



include_directories(
  /usr/local/include/iceoryx/v2.0.2
  mujoco/include
  mujoco/simulate
  src/lodepng
)
link_directories(mujoco/lib)

file(GLOB SIM_SRC
  src/joystick/joystick.cc

  # exclude mujoco/simulate/main.cc
  mujoco/simulate/glfw_*.cc
  mujoco/simulate/platform_*.cc
  mujoco/simulate/simulate.cc
  src/lodepng/lodepng.cpp
)

link_libraries(
  pthread
  mujoco
  glfw
  yaml-cpp
  unitree_sdk2
  boost_program_options
  fmt
  ${rclcpp_LIBRARIES}
  ${sensor_msgs_LIBRARIES}
  ${tf2_ros_LIBRARIES}
  ${tf2_geometry_msgs_LIBRARIES}
)


add_executable(unitree_mujoco ${SIM_SRC} src/main.cc)
ament_target_dependencies(unitree_mujoco rclcpp rcl_interfaces sensor_msgs tf2_ros tf2_geometry_msgs)
target_link_libraries(unitree_mujoco OpenMP::OpenMP_CXX)


add_executable(jstest src/joystick/jstest.cc src/joystick/joystick.cc)

# Added install target so colcon can move the binaries to the install folder
install(TARGETS unitree_mujoco jstest DESTINATION bin)
