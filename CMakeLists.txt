cmake_minimum_required(VERSION 2.8.3)
project(cgal_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

set(VERSION 4.11.0)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(cgal_src
  GIT_REPOSITORY git@github.com:CGAL/cgal.git
  GIT_TAG releases/CGAL-4.11
  UPDATE_COMMAND ""
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
    -DCMAKE_BUILD_TYPE=Release
    -DBUILD_SHARED_LIBS=ON
  BUILD_COMMAND make
  INSTALL_COMMAND make install
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} cgal_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libCGAL${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          CFG_EXTRAS cgal-extras.cmake)
