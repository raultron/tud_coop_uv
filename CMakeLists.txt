cmake_minimum_required(VERSION 2.8.3)
project(tud_coop_uv)

add_definitions(-std=c++11)
set (CMAKE_CXX_STANDARD 11)
set (CMAKE_CXX_FLAGS_DEBUG "-O0")
set (CMAKE_C_FLAGS_DEBUG "-O0")


## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
    dynamic_reconfigure
    geometry_msgs
    tf2_geometry_msgs
    roscpp
    rospy
    std_srvs
    std_msgs
    nav_msgs
    tf
    tf2
    message_generation
)

find_package(CGAL REQUIRED COMPONENTS Core)

add_service_files(
  DIRECTORY srv
  FILES
  SetController.srv
)
#For Qt
#find_package(Qt4 COMPONENTS QtCore QtGui)
#INCLUDE(${QT_USE_FILE})
#ADD_DEFINITIONS(${QT_DEFINITIONS})

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
#add_service_files(
#   FILES
#   SetController.srv
#)

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
generate_messages(DEPENDENCIES  std_msgs)


################################################
## Declare ROS dynamic reconfigure parameters ##
################################################

## To declare and build dynamic reconfigure parameters within this
## package, follow these steps:
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for "dynamic_reconfigure"
## * In this file (CMakeLists.txt):
##   * add "dynamic_reconfigure" to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * uncomment the "generate_dynamic_reconfigure_options" section below
##     and list every .cfg file to be processed

## Generate dynamic reconfigure parameters in the 'cfg' folder
# generate_dynamic_reconfigure_options(
#   cfg/DynReconf1.cfg
#   cfg/DynReconf2.cfg
# )

## Generate dynamic parameters
generate_dynamic_reconfigure_options(
  cfg/tracking_dynamic_param_config.cfg
)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
#  LIBRARIES coop_uv
  CATKIN_DEPENDS roscpp rospy std_msgs geometry_msgs nav_msgs tf2 tf std_srvs tf2_geometry_msgs message_runtime
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include)
include_directories(${catkin_INCLUDE_DIRS})
include(${CGAL_USE_FILE})
## Declare a cpp library
# add_library(coop_uv
#   src/${PROJECT_NAME}/coop_uv.cpp
# )

set(DEFINETARGET_SOURCE_FILES
    src/definetarget.cpp
    src/definetargetnode.cpp
)

set(DEFINETARGET_HEADER_FILES
    include/tud_coop_uv/definetargetnode.hpp
)

set(TRAKING_SOURCE_FILES
    src/tracking.cpp
    src/trackingnode.cpp
    src/filter.cpp
)

set(TRAKING_HEADER_FILES
    include/tud_coop_uv/trackingnode.hpp
    include/tud_coop_uv/filter.hpp
)

set(COVERAGE_SOURCE_FILES
    src/coverage.cpp
    src/coveragenode.cpp
)

set(COVERAGE_HEADER_FILES
    include/tud_coop_uv/coveragenode.hpp
)

set(MERGE_SOURCE_FILES
    src/merge.cpp
    src/mergenode.cpp
)

set(MERGE_HEADER_FILES
    include/tud_coop_uv/mergenode.hpp
)


## Declare a cpp executable
add_executable(define_target ${DEFINETARGET_SOURCE_FILES} ${DEFINETARGET_HEADER_FILES})

add_executable(tracking ${TRAKING_SOURCE_FILES} ${TRAKING_HEADER_FILES})

add_executable(coverage ${COVERAGE_SOURCE_FILES} ${COVERAGE_HEADER_FILES})

add_executable(merge ${MERGE_SOURCE_FILES} ${MERGE_HEADER_FILES})

add_executable(joy_control src/joy_control.cpp)

#add_executable(mainQt src/mainQt.cpp)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
##add_dependencies(coop_uv_node coop_uv_generate_messages_cpp)
add_dependencies(define_target tud_coop_uv_generate_messages_cpp)
add_dependencies(merge tud_coop_uv_generate_messages_cpp)
add_dependencies(tracking tud_coop_uv_generate_messages_cpp ${PROJECT_NAME}_gencfg)
add_dependencies(joy_control tud_coop_uv_generate_messages_cpp)
add_dependencies(coverage tud_coop_uv_generate_messages_cpp)

## Specify libraries to link a library or executable target against
target_link_libraries(define_target ${catkin_LIBRARIES})

target_link_libraries(tracking ${catkin_LIBRARIES})

target_link_libraries(coverage ${catkin_LIBRARIES} ${CGAL})

target_link_libraries(merge ${catkin_LIBRARIES})

target_link_libraries(joy_control ${catkin_LIBRARIES})

#target_link_libraries(mainQt ${catkin_LIBRARIES} ${QT_LIBRARIES})

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS coop_uv coop_uv_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_coop_uv.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)

##################
# FOR QT CREATOR
##################
file(GLOB_RECURSE QT_MOC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS include/tud_coop_uv/*.hpp)
FILE(GLOB_RECURSE INC_ALL "include/tud_coop_uv/*.hpp")
FILE(GLOB_RECURSE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS src/*.cpp)
