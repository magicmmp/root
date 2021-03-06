############################################################################
# CMakeLists.txt file for building ROOT graf2d/cocoa package
# @author Pere Mato, CERN
############################################################################

add_definitions("-ObjC++")

if (cxxmodules)
  # FIXME: Disable modules for ObjC/ObjC++. Modules for cocoa and quarts have to
  # be treated in a special way. Eg. it seems that they cannot be nested in the
  # ROOT top-most module, because an implicit build of this module in ObjC TU
  # causes issues in TBonjourBrowser (when incluing dns_sd.h) on OSX.
  string(REPLACE "${ROOT_CXXMODULES_CXXFLAGS}" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
  string(REPLACE "${ROOT_CXXMODULES_CFLAGS}" "" CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
endif(cxxmodules)

include_directories(${FREETYPE_INCLUDE_DIRS})

ROOT_STANDARD_LIBRARY_PACKAGE(GCocoa
                              HEADERS T*.h
                              SOURCES *.mm
                              DEPENDENCIES Gui GQuartz
                              LIBRARIES  "-framework Cocoa" "-framework OpenGL" ${FREETYPE_LIBRARIES})
if(NOT opengl) # special case when cocoa is enabled but not opengl (i.e. gminimal=ON)
  set_source_files_properties(src/TGCocoa.mm PROPERTIES 
                             COMPILE_FLAGS "-I${CMAKE_SOURCE_DIR}/graf3d/gl/inc -I${CMAKE_SOURCE_DIR}/graf3d/glew/inc")
endif()
# We know GL is deprecated on macOS. And our answer is WebGUI/Graphics.
set_target_properties(GCocoa PROPERTIES COMPILE_FLAGS "-Wdeprecated-declarations")