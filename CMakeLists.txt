##---------------------------------------------------------------------------
## Author:      Sean D'Epagnier
## Copyright:   
## License:     GPLv3+
##---------------------------------------------------------------------------
 
# define minimum cmake version
CMAKE_MINIMUM_REQUIRED(VERSION 2.6.2)

PROJECT(autopilot_route_pi)
SET(PACKAGE_NAME autopilot_route_pi)
SET(VERBOSE_NAME Autopilot_Route)
SET(TITLE_NAME AUTOPILOT_ROUTE)
SET(CPACK_PACKAGE_CONTACT "Sean D'Epagnier ")

SET(BUNDLE_DATA FALSE)

INCLUDE( VERSION.cmake )
SET(VERSION_MAJOR ${PLUGIN_VERSION_MAJOR})
SET(VERSION_MINOR ${PLUGIN_VERSION_MINOR})
SET(VERSION_PATCH ${PLUGIN_VERSION_PATCH})
SET(VERSION_DATE ${PLUGIN_VERSION_DATE})


SET(PLUGIN_VERSION "${PLUGIN_VERSION_MAJOR}.${PLUGIN_VERSION_MINOR}.${PLUGIN_VERSION_PATCH}" )

INCLUDE("cmake/PluginConfigure.cmake")

SET(SRC_AUTOPILOT_ROUTE
            src/autopilot_route_pi.cpp
            src/autopilot_route_pi.cpp
            src/PreferencesDialog.cpp
            src/AutopilotRouteUI.cpp
            src/concanv.cpp
            src/computation.cpp
            src/georef.c
            src/icons.cpp
            src/ODAPI.h
            src/apdc.cpp
            src/TexFont.cpp
	)

INCLUDE("cmake/PluginNMEA.cmake")
INCLUDE("cmake/PluginJSON.cmake")

IF(UNIX)
 INCLUDE("cmake/FindTinyXML.cmake")
 FIND_PACKAGE(TinyXML QUIET)
ENDIF(UNIX)

IF(TINYXML_FOUND)
 message (STATUS "Building with system tinyxml")
 INCLUDE_DIRECTORIES(${TINYXML_INCLUDE_DIR})
 ADD_LIBRARY(${PACKAGE_NAME} SHARED ${SRC_AUTOPILOT_ROUTE} ${SRC_JSON} ${SRC_NMEA0183})
 TARGET_LINK_LIBRARIES(${PACKAGE_NAME} ${TINYXML_LIBRARIES})
ELSE(TINYXML_FOUND)
 message (STATUS "Building with embedded tinyxml")
 INCLUDE_DIRECTORIES(src/tinyxml/)
 SET(SRC_LTINYXML
            src/tinyxml/tinyxml.cpp
            src/tinyxml/tinyxmlparser.cpp
            src/tinyxml/tinyxmlerror.cpp
 )
 ADD_LIBRARY(${PACKAGE_NAME} SHARED ${SRC_AUTOPILOT_ROUTE} ${SRC_JSON} ${SRC_NMEA0183} ${SRC_LTINYXML})
ENDIF(TINYXML_FOUND)
ADD_DEFINITIONS(-DTIXML_USE_STL)

INCLUDE("cmake/PluginInstall.cmake")
INCLUDE("cmake/PluginLocalization.cmake")
INCLUDE("cmake/PluginPackage.cmake")
