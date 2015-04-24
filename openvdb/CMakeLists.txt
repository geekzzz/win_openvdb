
# Windows OpenVDB cmake
# for win_openvdb fork by: lithozine (ramahoetzlein@gmail.com)
# original by yue.nicholas@gmail.com


macro (POST_COPY SRC_TARGET SRC_PATH SRC_MATCH DEST_DIR)
  # Make sure dir exists
  MAKE_DIRECTORY ( "${DEST_DIR}" )
  
  # Locate all files matching pattern
  message ( STATUS "Packaging for /include: ${SRC_PATH}/${SRC_MATCH} -> ${DEST_DIR}" )    
  if ( "${SRC_MATCH}" MATCHES "(.*)\\*(.*)" )  
     # wildcard given, find all matching files (MUST EXIST NOW, AT CMAKE TIME, BEFORE BUILD)
     FILE ( GLOB src_list ABSOLUTE "${SRC_PATH}/${SRC_MATCH}" )
  else ()
     # specific file, just copy it
     SET ( src_list "${SRC_PATH}/${SRC_MATCH}")
  endif ()
  
  # Add post-build copy
  foreach ( src_file ${src_list})
    ADD_CUSTOM_COMMAND ( TARGET ${SRC_TARGET}
        POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${src_file} ${DEST_DIR}
    )
  endforeach () 
endmacro ()


CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

PROJECT ( OpenVDB )
GET_FILENAME_COMPONENT ( BUILD_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../../build/" REALPATH )
GET_FILENAME_COMPONENT ( SOURCE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../" REALPATH )
GET_FILENAME_COMPONENT ( CMAKE_PATH  "${CMAKE_CURRENT_SOURCE_DIR}/../cmake_modules/" REALPATH )

SET ( BASE_BUILD_PATH  ${BUILD_PATH} CACHE PATH "Base build output path.." )
SET ( BASE_SOURCE_PATH  ${SOURCE_PATH} CACHE PATH "Base source code path..")
SET ( CMAKE_MODULE_PATH ${CMAKE_PATH} CACHE PATH "Path to Find.cmake modules" )

FIND_PACKAGE ( GLFW3 REQUIRED )
IF (WIN32)
  FIND_PACKAGE ( GLEW REQUIRED )
ENDIF()

# Better search hints for Boost 
  FILE ( GLOB children RELATIVE ${BASE_BUILD_PATH} ${BASE_BUILD_PATH}/*)
  UNSET ( BOOST_ROOT )
  FOREACH(subdir ${children})
      IF ( "${subdir}" MATCHES "boost(.*)" )
        SET ( BOOST_ROOT ${BASE_BUILD_PATH}/${subdir}/ CACHE PATH "Path to Boost")
        SET ( BOOST_LIBRARYDIR "${BOOST_ROOT}/lib64-msvc-10.0" )
     ENDIF()
  ENDFOREACH()    
  
FIND_PACKAGE ( Boost REQUIRED COMPONENTS iostreams system thread )

FIND_PACKAGE ( ILMBase REQUIRED )
FIND_PACKAGE ( OpenEXR REQUIRED )
FIND_PACKAGE ( TBB REQUIRED )
FIND_PACKAGE ( ZLIB REQUIRED )
FIND_PACKAGE ( OpenGL REQUIRED )
# FIND_PACKAGE ( Blosc REQUIRED )
# FIND_PACKAGE ( Doxygen REQUIRED )
IF (APPLE)
  FIND_LIBRARY ( COCOA_LIBRARY Cocoa )
  FIND_LIBRARY ( IOKIT_LIBRARY IOKit )
  FIND_LIBRARY ( COREVIDEO_LIBRARY CoreVideo )
ENDIF ()

IF ( UNIX AND NOT APPLE )
  MESSAGE ( "UNIX" )
  SET ( XI_LIBRARY Xi )
  SET ( XRANDR_LIBRARY Xrandr )
ENDIF ()

IF ( GLFW_FOUND )
  SET ( OPENVDB_USE_GLFW_FLAG "-DOPENVDB_USE_GLFW_3" CACHE STRING "Use GLFW3 for OpenVDB")
ENDIF ()

# MESSAGE ( "ZLIB_INCLUDE_DIR = ${ZLIB_INCLUDE_DIR}")

INCLUDE_DIRECTORIES ( ${Boost_INCLUDE_DIR} )
INCLUDE_DIRECTORIES ( ${ILMBASE_INCLUDE_DIR} )
INCLUDE_DIRECTORIES ( ${OPENEXR_INCLUDE_DIR} )
INCLUDE_DIRECTORIES ( ${TBB_INCLUDE_DIR} )
INCLUDE_DIRECTORIES ( ${OPENGL_INCLUDE_DIR} )
INCLUDE_DIRECTORIES ( ${ZLIB_INCLUDE_DIR} )
INCLUDE_DIRECTORIES ( ${GLFW_INCLUDE_DIRS} )
IF (WIN32)
  INCLUDE_DIRECTORIES ( ${GLEW_INCLUDE_DIR} )
ENDIF ()
INCLUDE_DIRECTORIES ( .. ) # So they can simulate referencing installed headers
INCLUDE_DIRECTORIES ( . ) # So they can simulate referencing installed headers

IF (WIN32)
  # Because of implicit linking!
  LINK_DIRECTORIES ( ${Boost_LIBRARY_DIR_RELEASE} )
ENDIF ()

INCLUDE_DIRECTORIES ( ${BLOSC_INCLUDE_DIR} )

IF (WIN32)
  ADD_DEFINITIONS ( -D_WIN32 -DNOMINMAX -DOPENVDB_STATICLIB -DOPENEXR_DLL )
  ADD_DEFINITIONS ( ${OPENVDB_USE_GLFW_FLAG} )
ELSE ()
  ADD_DEFINITIONS ( -pthread -fPIC )
ENDIF ()

# Working around hardcoding of path at DWA
FILE ( GLOB VIEWER_SOURCE_FILES RELATIVE ${CMAKE_SOURCE_DIR} viewer/* )
FILE ( MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/dwa/openvdb_viewer )
FILE ( COPY ${VIEWER_SOURCE_FILES} DESTINATION ${CMAKE_BINARY_DIR}/dwa/openvdb_viewer )
INCLUDE_DIRECTORIES ( ${CMAKE_BINARY_DIR}/dwa )

SET ( OPENVDB_LIBRARY_SOURCE_FILES
  Grid.cc
  io/Archive.cc
  io/Compression.cc
  io/File.cc
  io/GridDescriptor.cc
  io/Queue.cc
  io/Stream.cc
  io/TempFile.cc
  math/Hermite.cc
  math/Maps.cc
  math/Proximity.cc
  math/QuantizedUnitVec.cc
  math/Transform.cc
  metadata/Metadata.cc
  metadata/MetaMap.cc
  openvdb.cc
  Platform.cc
  util/Formats.cc
  util/Util.cc
  )
  
  #---- removed blosc dependency (Rama)
  # COMPILE_FLAGS "-DOPENVDB_PRIVATE -DOPENVDB_USE_BLOSC ${OPENVDB_USE_GLFW_FLAG}"

SET_SOURCE_FILES_PROPERTIES ( ${OPENVDB_LIBRARY_SOURCE_FILES}
  PROPERTIES  
  COMPILE_FLAGS "-DOPENVDB_PRIVATE ${OPENVDB_USE_GLFW_FLAG} /bigobj"
)

ADD_LIBRARY ( openvdb STATIC
  ${OPENVDB_LIBRARY_SOURCE_FILES}
  )
if ( NOT EXISTS ${OPENEXR_ILMIMF_LIBRARY_DEBUG} )
  SET ( OPENEXR_ILMIMF_LIBRARY_DEBUG ${OPENEXR_ILMIMF_LIBRARY_RELEASE} )
endif()

  

TARGET_LINK_LIBRARIES ( openvdb debug
  ${Boost_SYSTEM_LIBRARY_DEBUG}
  ${Boost_THREAD_LIBRARY_DEBUG}
  ${TBB_LIBRARY_DEBUG}
  ${ILMBASE_HALF_LIBRARY_DEBUG}
  ${OPENEXR_ILMIMF_LIBRARY_DEBUG}
  ${ZLIB_LIBRARY}
  ${GLFW_LIBRARY}
  )
TARGET_LINK_LIBRARIES ( openvdb optimized
  ${Boost_SYSTEM_LIBRARY_RELEASE}
  ${Boost_THREAD_LIBRARY_RELEASE}
  ${TBB_LIBRARY}
  ${ILMBASE_HALF_LIBRARY_RELEASE}
  ${OPENEXR_ILMIMF_LIBRARY_RELEASE}
  ${ZLIB_LIBRARY}
  ${GLFW_LIBRARY}
  )
  

SET ( VDB_PRINT_SOURCE_FILES  cmd/openvdb_print/main.cc )
SET_SOURCE_FILES_PROPERTIES ( ${VDB_PRINT_SOURCE_FILES}
  PROPERTIES
  COMPILE_FLAGS "${OPENVDB_USE_GLFW_FLAG}"
)

ADD_EXECUTABLE ( vdb_print
  ${VDB_PRINT_SOURCE_FILES}
  )

TARGET_LINK_LIBRARIES ( vdb_print
  openvdb
  ${BLOSC_blosc_LIBRARY} 
  )

SET ( VDB_RENDER_SOURCE_FILES cmd/openvdb_render/main.cc )



SET_SOURCE_FILES_PROPERTIES ( ${VDB_RENDER_SOURCE_FILES}
  PROPERTIES
  COMPILE_FLAGS "${OPENVDB_USE_GLFW_FLAG} /bigobj"
)

ADD_EXECUTABLE ( vdb_render
  ${VDB_RENDER_SOURCE_FILES}
  )

TARGET_LINK_LIBRARIES ( vdb_render debug
  openvdb  
  ${Ilmbase_ILMTHREAD_LIBRARY_DEBUG}
  ${Ilmbase_IEX_LIBRARY_DEBUG}
  ${OPENEXR_ILMIMF_LIBRARY_DEBUG}
  )
  
TARGET_LINK_LIBRARIES ( vdb_render optimized
  openvdb  
  ${Ilmbase_ILMTHREAD_LIBRARY_RELEASE}
  ${Ilmbase_IEX_LIBRARY_RELEASE}
  ${OPENEXR_ILMIMF_LIBRARY_RELEASE}
  )
  
# Copy required dlls to output path
POST_COPY ( vdb_render ${OPENEXR_HOME}/lib IlmImf.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib Half.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib Iex.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib Imath.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib IlmThread.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${ZLIB_ROOT_DIR}/lib zlib.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${TBB_BINARY_DIR} *.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${Boost_LIBRARY_DIR_RELEASE} boost_system*.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_render ${Boost_LIBRARY_DIR_RELEASE} boost_thread*.dll "${CMAKE_BINARY_DIR}/Release" )

POST_COPY ( vdb_render ${OPENEXR_HOME}/lib IlmImf.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib Half.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib Iex.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib Imath.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${ILMBASE_HOME}/lib IlmThread.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${ZLIB_ROOT_DIR}/lib zlib.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${TBB_BINARY_DIR} *.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${Boost_LIBRARY_DIR_DEBUG} boost_system*.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_render ${Boost_LIBRARY_DIR_DEBUG} boost_thread*.dll "${CMAKE_BINARY_DIR}/Debug" )

  
SET ( VDB_VIEW_SOURCE_FILES
  cmd/openvdb_view/main.cc
  viewer/Camera.cc
  viewer/ClipBox.cc
  viewer/Font.cc
  viewer/RenderModules.cc
  viewer/Viewer.cc
 )
SET_SOURCE_FILES_PROPERTIES ( ${VDB_VIEW_SOURCE_FILES}
  PROPERTIES
  COMPILE_FLAGS "${OPENVDB_USE_GLFW_FLAG} -DGL_GLEXT_PROTOTYPES=1 /bigobj"
)
ADD_EXECUTABLE ( vdb_view
  ${VDB_VIEW_SOURCE_FILES}
  )

TARGET_LINK_LIBRARIES ( vdb_view
  openvdb
  ${OPENGL_gl_LIBRARY}
  ${OPENGL_glu_LIBRARY}
  ${COCOA_LIBRARY}
  ${IOKIT_LIBRARY}
  ${COREVIDEO_LIBRARY}
  ${GLFW_LIBRARY}
  ${XI_LIBRARY}
  ${XRANDR_LIBRARY}
  ${GLEW_LIBRARY}
  )

# DLLs for vdb_view
POST_COPY ( vdb_view "${GLEW_ROOT_DIR}/bin/Release/x64" glew32.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_view ${Boost_LIBRARY_DIR_RELEASE} boost_thread*.dll "${CMAKE_BINARY_DIR}/Release" )
POST_COPY ( vdb_view ${Boost_LIBRARY_DIR_RELEASE} boost_chrono*.dll "${CMAKE_BINARY_DIR}/Release" )

POST_COPY ( vdb_view "${GLEW_ROOT_DIR}/bin/Release/x64" glew32.dll "${CMAKE_BINARY_DIR}/Debug" )
POST_COPY ( vdb_view ${Boost_LIBRARY_DIR_DEBUG} boost_chrono*.dll "${CMAKE_BINARY_DIR}/Debug" )

# Doxygen docmentation
FILE ( READ ${CMAKE_SOURCE_DIR}/doxygen-config DOXYGEN_CONFIG_CONTENT )
FILE ( WRITE ${CMAKE_CURRENT_BINARY_DIR}/cmake-doxygen-config ${DOXYGEN_CONFIG_CONTENT} )
FILE ( APPEND ${CMAKE_CURRENT_BINARY_DIR}/cmake-doxygen-config "OUTPUT_DIRECTORY=${CMAKE_BINARY_DIR}/doc\n" )

ADD_CUSTOM_TARGET ( doc ALL
  ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/cmake-doxygen-config
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  COMMENT "Generating API documentation with Doxygen" VERBATIM
)

INSTALL ( DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/doc/html/ DESTINATION doc)

# Installation
INSTALL ( TARGETS
  vdb_view
  vdb_print
  vdb_render
  DESTINATION
  bin
  )

INSTALL ( TARGETS
  openvdb
  DESTINATION
  lib
  )

INSTALL ( DIRECTORY ${CMAKE_SOURCE_DIR} DESTINATION include
          FILES_MATCHING PATTERN "*.h")
