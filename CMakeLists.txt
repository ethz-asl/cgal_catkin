cmake_minimum_required(VERSION 2.8.3)
project(cgal_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

set(VERSION 5.0.3)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

set(CGAL_VERSION 5.0.3)
ExternalProject_Add(cgal
  URL https://github.com/CGAL/cgal/archive/releases/CGAL-${CGAL_VERSION}.tar.gz
  UPDATE_COMMAND ""
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX}
    -DWITH_CGAL_Qt5:BOOL=OFF
    -DWITH_CGAL_ImageIO:BOOL=OFF
    -DCMAKE_BUILD_TYPE:STRING=Release
  BUILD_COMMAND $(MAKE)
  INSTALL_COMMAND $(MAKE) install
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} cgal)

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          CFG_EXTRAS cgal-extras.cmake)
cs_install()
