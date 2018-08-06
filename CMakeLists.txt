cmake_minimum_required(VERSION 2.8.3)
project(cgal_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

set(VERSION 4.11)
ExternalProject_Add(cgal_src
  GIT_REPOSITORY git@github.com:CGAL/cgal-public-dev.git
  GIT_TAG 8933fbce58a0fd57d1c0b02c735fa41a5ba594a3
  UPDATE_COMMAND ""
  #PATCH_COMMAND patch -p0 < ${CMAKE_CURRENT_SOURCE_DIR}/vsa_dependency.patch
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
    -DCMAKE_BUILD_TYPE=Release
    -DBUILD_SHARED_LIBS=ON
    -DWITH_CGAL_Qt5:BOOL=OFF
    -DWITH_CGAL_ImageIO:BOOL=OFF
  BUILD_COMMAND $(MAKE)
  INSTALL_COMMAND $(MAKE) install
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} cgal_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libCGAL${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          CFG_EXTRAS cgal-extras.cmake)
