cmake_minimum_required(VERSION 2.8)
project(octomap_server)

set(PACKAGE_DEPENDENCIES
  roscpp
  visualization_msgs
  sensor_msgs
  pcl_ros
  pcl_conversions
  nav_msgs
  std_msgs
  std_srvs
  octomap_ros
  octomap_msgs
  dynamic_reconfigure
  nodelet
)


find_package(catkin REQUIRED COMPONENTS ${PACKAGE_DEPENDENCIES})

find_package(octomap REQUIRED)
set(OCTOMAP_INCLUDE_DIRS "/home/tekniker/Software/octomap/octomap/include")
set(OCTOMAP_LIBRARY_DIRS "/home/tekniker/Software/octomap/octomap/lib")
set(OCTOMAP_LIBRARIES
  "/home/tekniker/Software/octomap/octomap/lib/liboctomap.a"
  "/home/tekniker/Software/octomap/octomap/lib/liboctomath.a"
)
add_definitions(-DOCTOMAP_NODEBUGOUT)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${OCTOMAP_INCLUDE_DIRS}
)


generate_dynamic_reconfigure_options(cfg/OctomapServer.cfg)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS ${PACKAGE_DEPENDENCIES}
#  DEPENDS OCTOMAP
)

set(LINK_LIBS
  ${OCTOMAP_LIBRARIES}
  ${catkin_LIBRARIES}
)

MESSAGE(OCTOMAP_INCLUDE_DIRS:${OCTOMAP_INCLUDE_DIRS})
MESSAGE(OCTOMAP_LIBRARY_DIRS:${OCTOMAP_LIBRARY_DIRS})
MESSAGE(OCTOMAP_LIBRARIES:${OCTOMAP_LIBRARIES})

add_library(${PROJECT_NAME} src/OctomapServer.cpp src/OctomapServerMultilayer.cpp src/TrackingOctomapServer.cpp)
target_link_libraries(${PROJECT_NAME} ${LINK_LIBS})
add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}_gencfg)

add_library(${PROJECT_NAME}_color src/OctomapServer.cpp src/OctomapServerMultilayer.cpp src/TrackingOctomapServer.cpp)
target_link_libraries(${PROJECT_NAME}_color ${LINK_LIBS})
add_dependencies(${PROJECT_NAME}_color ${PROJECT_NAME}_gencfg)
target_compile_definitions(${PROJECT_NAME}_color PUBLIC COLOR_OCTOMAP_SERVER)

add_library(${PROJECT_NAME}_uncertainty src/OctomapServer.cpp src/OctomapServerMultilayer.cpp src/TrackingOctomapServer.cpp)
target_link_libraries(${PROJECT_NAME}_uncertainty ${LINK_LIBS})
add_dependencies(${PROJECT_NAME}_uncertainty ${PROJECT_NAME}_gencfg)
target_compile_definitions(${PROJECT_NAME}_uncertainty PUBLIC UNCERTAINTY_OCTOMAP_SERVER)

add_executable(octomap_server_node src/octomap_server_node.cpp)
target_link_libraries(octomap_server_node ${PROJECT_NAME} ${LINK_LIBS})

add_executable(octomap_color_server_node src/octomap_server_node.cpp)
target_link_libraries(octomap_color_server_node ${PROJECT_NAME}_color ${LINK_LIBS})

add_executable(octomap_uncertainty_server_node src/octomap_server_node.cpp)
target_link_libraries(octomap_uncertainty_server_node ${PROJECT_NAME}_uncertainty ${LINK_LIBS})

add_executable(octomap_server_static src/octomap_server_static.cpp)
target_link_libraries(octomap_server_static ${PROJECT_NAME} ${LINK_LIBS})

add_executable(octomap_server_multilayer src/octomap_server_multilayer.cpp)
target_link_libraries(octomap_server_multilayer ${PROJECT_NAME} ${LINK_LIBS})

add_executable(octomap_saver src/octomap_saver.cpp)
target_link_libraries(octomap_saver ${PROJECT_NAME} ${LINK_LIBS})

add_executable(octomap_tracking_server_node src/octomap_tracking_server_node.cpp)
target_link_libraries(octomap_tracking_server_node ${PROJECT_NAME} ${LINK_LIBS})

# Nodelet
add_library(octomap_server_nodelet src/octomap_server_nodelet.cpp)
target_link_libraries(octomap_server_nodelet ${PROJECT_NAME} ${LINK_LIBS})

# install targets:
install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_color ${PROJECT_NAME}_uncertainty
  octomap_server_node
  octomap_color_server_node
  octomap_uncertainty_server_node
  octomap_server_static
  octomap_server_multilayer
  octomap_saver
  octomap_tracking_server_node
  octomap_server_nodelet
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  PATTERN ".svn" EXCLUDE
)

install(FILES nodelet_plugins.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
