cmake_minimum_required(VERSION 3.16)
project(stand_go2_cpp)

list(APPEND CMAKE_PREFIX_PATH "/opt/unitree_robotics/lib/cmake")
find_package(unitree_sdk2 REQUIRED)

add_executable(stand_go2_cpp stand_go2.cpp)
target_link_libraries(stand_go2_cpp unitree_sdk2)

SET(CMAKE_BUILD_TYPE Release)

# Added install target so colcon can move the binary to the install folder
install(TARGETS stand_go2_cpp DESTINATION bin)