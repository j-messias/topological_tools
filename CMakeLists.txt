cmake_minimum_required(VERSION 2.8.3)

project(topological_tools)

find_package(catkin REQUIRED COMPONENTS
                             roscpp
                             message_generation)

find_package( SDL )
find_package( SDL_image )

add_message_files(FILES
                  PoseLabel.msg)

generate_messages(DEPENDENCIES std_msgs)

if ( SDLIMAGE_FOUND )

add_library(topological_tools
            src/topological_node.cpp
            src/topological_map.cpp
            src/pose_labeler.cpp)

else ( SDLIMAGE_FOUND )

add_library(topological_tools
            src/topological_node.cpp
            src/topological_map.cpp)
            
message(STATUS "WARNING: The SDL_image libary was not found on this system.")
message(STATUS "WARNING: You won't be able to use the pose_labeler tool or its dependencies.")

endif ( SDLIMAGE_FOUND )
            
catkin_package(
  INCLUDE_DIRS include
  LIBRARIES topological_tools
  CATKIN_DEPENDS roscpp
)

include_directories(include ${catkin_INCLUDE_DIRS})

add_dependencies(topological_tools ${PROJECT_NAME}_generate_messages_cpp)

if ( SDLIMAGE_FOUND )

target_link_libraries(topological_tools ${catkin_LIBRARIES} ${SDLIMAGE_LIBRARY})

add_executable(pose_labeler_node 
               src/pose_labeler_node.cpp)

add_dependencies(pose_labeler_node ${PROJECT_NAME}_generate_messages_cpp)

target_link_libraries(pose_labeler_node ${catkin_LIBRARIES} ${SDLIMAGE_LIBRARY} topological_tools)

else ( SDLIMAGE_FOUND )

target_link_libraries(topological_tools ${catkin_LIBRARIES})

endif ( SDLIMAGE_FOUND )
