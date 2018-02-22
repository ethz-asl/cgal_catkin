cmake_minimum_required(VERSION 2.8.3)
project(cgal_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

set(VERSION 4.11.0)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

set(CGAL_VERSION 4.11)
ExternalProject_Add(cgal_src
  URL https://github.com/CGAL/cgal/archive/releases/CGAL-${CGAL_VERSION}.tar.gz
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND
    cd ../cgal_src/ &&
    cmake
      -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
      -DWITH_CGAL_Qt5:BOOL=OFF
      -DWITH_CGAL_ImageIO:BOOL=OFF
      -DCMAKE_BUILD_TYPE:STRING=Release
      .
  BUILD_COMMAND
    cd ../cgal_src/ &&
    $(MAKE)
  INSTALL_COMMAND
    cd ../cgal_src/ &&
    $(MAKE) install
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} cgal_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libCGAL${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          CFG_EXTRAS cgal-extras.cmake)
