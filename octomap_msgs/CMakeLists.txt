cmake_minimum_required(VERSION 2.8)
project(octomap_msgs)

find_package(catkin REQUIRED COMPONENTS message_generation geometry_msgs std_msgs)

include_directories(include)

add_message_files(
  DIRECTORY msg
  FILES Octomap.msg OctomapWithPose.msg
  )

add_service_files(
  DIRECTORY srv
  FILES GetOctomap.srv BoundingBoxQuery.srv EstimateGain.srv GetAABBoxValue.srv GetValue.srv
)

generate_messages(DEPENDENCIES std_msgs geometry_msgs)

catkin_package(DEPENDS message_runtime geometry_msgs std_msgs
               INCLUDE_DIRS include
)

install(DIRECTORY include/
        DESTINATION include
        FILES_MATCHING PATTERN "*.h"
        PATTERN ".svn" EXCLUDE)
