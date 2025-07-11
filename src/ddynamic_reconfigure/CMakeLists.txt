cmake_minimum_required(VERSION 2.8.3)
project(ddynamic_reconfigure)

find_package(catkin REQUIRED COMPONENTS
  dynamic_reconfigure
  roscpp
)

if(NOT DEFINED CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS roscpp dynamic_reconfigure
)

include_directories(
  include
)
include_directories(
  SYSTEM 
  ${catkin_INCLUDE_DIRS}
)

add_library(${PROJECT_NAME} src/ddynamic_reconfigure.cpp)
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES})
#add_executable(dynamic_reconfigure_node src/dynamic_reconfigure_node.cpp)
#target_link_libraries(dynamic_reconfigure_node ${catkin_LIBRARIES})

#make sure configure headers are built before any node using them
#add_dependencies(dynamic_reconfigure_node ${PROJECT_NAME}_gencfg)

#############  Test #################
add_executable(fake_dynamic_reconfigure_server test/fake_dynamic_reconfigure_server.cpp)
target_link_libraries(fake_dynamic_reconfigure_server ${PROJECT_NAME} ${catkin_LIBRARIES})

add_executable(test_bool_dynamic_reconfigure_server test/test_bool_dynamic_reconfigure_server.cpp)
target_link_libraries(test_bool_dynamic_reconfigure_server ${PROJECT_NAME} ${catkin_LIBRARIES})

install(TARGETS ${PROJECT_NAME}
   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

#############
## Testing ##
#############

if (CATKIN_ENABLE_TESTING)
    find_package(rostest REQUIRED)
    if (NOT WIN32)
    add_rostest_gmock(ddynamic_reconfigure-test test/ddynamic_reconfigure.test test/test_ddynamic_reconfigure.cpp)
    target_link_libraries(ddynamic_reconfigure-test
                                ${PROJECT_NAME})
    endif()
    
    add_executable(ddynamic_reconfigure_auto_update_test
        test/ddynamic_reconfigure_auto_update_test.cpp)
    target_link_libraries(ddynamic_reconfigure_auto_update_test ${PROJECT_NAME}
        ${catkin_LIBRARIES})

endif(CATKIN_ENABLE_TESTING)
