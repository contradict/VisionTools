CMAKE_MINIMUM_REQUIRED(VERSION 2.8.3)
SET(PROJECT_NAME VisionTools)
PROJECT(${PROJECT_NAME})
find_package(catkin REQUIRED
    COMPONENTS pangolin Sophus)

SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${PROJECT_SOURCE_DIR}/CMakeModules/")


SET (CMAKE_VERBOSE_MAKEFILE ON)

IF( NOT CMAKE_BUILD_TYPE )
   SET( CMAKE_BUILD_TYPE Release )
ENDIF()

IF (CMAKE_COMPILER_IS_GNUCXX )
   SET(CMAKE_CXX_FLAGS_DEBUG  "-O0 -g")
   SET(CMAKE_CXX_FLAGS_RELEASE "-O3 -g ")

  ADD_DEFINITIONS("-Wall -Werror -Wno-unused-variable
                   -Wno-unused-but-set-variable -Wno-unknown-pragmas ")
ENDIF()



SET (OpenCV_DIR /usr/local/share/OpenCV)
FIND_PACKAGE(OpenCV 2.4.7 REQUIRED)
FIND_PACKAGE(Eigen3 REQUIRED)

catkin_package(
    INCLUDE_DIRS ${PROJECT_SOURCE_DIR} ${Eigen_INCLUDE_DIR}
    LIBRARIES ${PROJECT_NAME} )

SET (LIB_NAMES GL glut)

FOREACH(lib ${LIB_NAMES})
  FIND_LIBRARY(LIB_${lib} ${lib})
  IF (LIB_${lib})
    MESSAGE(STATUS "found library '${lib}': ${LIB_${lib}}")
  ELSE (LIB_${lib})
    MESSAGE(FATAL_ERROR "cannot find library '${lib}' - this will not work ...")
  ENDIF (LIB_${lib})
  LIST(APPEND LIBS ${LIB_${lib}})
ENDFOREACH(lib)

SET (CLASSES  draw2d
              draw3d
              sample
              performance_monitor
              linear_camera)

SET (SOURCE_DIR "visiontools")

SET (SOURCES ${SOURCE_DIR}/gl_data.h
             ${SOURCE_DIR}/abstract_camera.h
             ${SOURCE_DIR}/accessor_macros.h
             ${SOURCE_DIR}/ringbuffer.h
             ${SOURCE_DIR}/stopwatch.h)

FOREACH(class ${CLASSES})
  LIST(APPEND SOURCES ${SOURCE_DIR}/${class}.cpp ${SOURCE_DIR}/${class}.h)
ENDFOREACH(class)

LINK_LIBRARIES (${PROJECT_NAME} ${LIBS})

INCLUDE_DIRECTORIES(
    ${catkin_INCLUDE_DIRS}
    ${Eigen_INCLUDE_DIR})
ADD_LIBRARY(${PROJECT_NAME} SHARED ${SOURCES})


INSTALL(DIRECTORY visiontools DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}/include FILES_MATCHING PATTERN "*.h" )
INSTALL(TARGETS ${PROJECT_NAME} LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})
