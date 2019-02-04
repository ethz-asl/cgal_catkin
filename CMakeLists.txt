cmake_minimum_required(VERSION 2.8.3)
project(cgal_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

set(VERSION 4.14.0)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(cgal_src
  GIT_REPOSITORY git@github.com:CGAL/cgal.git
  GIT_TAG 1ca9dbc514a4de9b214d2a6270a4b3294381948f
  UPDATE_COMMAND ""
  PATCH_COMMAND patch -p0 < ${CMAKE_CURRENT_SOURCE_DIR}/add_one_proxy_at_feature.patch
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
    -DCMAKE_BUILD_TYPE=Release
    -DBUILD_SHARED_LIBS=ON
    -DWITH_examples=ON
  BUILD_COMMAND make
  INSTALL_COMMAND make install
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} cgal_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libCGAL${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          CFG_EXTRAS cgal-extras.cmake)
