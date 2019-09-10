cmake_minimum_required(VERSION 3.11)
project(libOpusCodec VERSION 1.0.0 LANGUAGES CXX)

#Make sure that custom modules like FindRapidJSON are found
list(INSERT CMAKE_MODULE_PATH 0 ${CMAKE_SOURCE_DIR}/cmake)

##############################################
include(FetchContent)
FetchContent_Declare(
  vpp
  GIT_REPOSITORY https://github.com/CSIR-RTVC/vpp
)


# Declare dependencies
find_package(vpp 1.0.0)

##############################################
# Create target and set properties

SET(OPUS_LIB_HDRS
        ./include/OpusCodec/OpusCodec.h
        ./include/OpusCodec/OpusFactory.h
        ./src/StdAfx.h
        ./src/targetver.h
)

SET(OPUS_LIB_SRCS
        ./src/OpusCodec.cpp
        ./src/OpusFactory.cpp
        ./src/dllmain.cpp
        ./src/StdAfx.cpp
)
ADD_LIBRARY( OpusCodec SHARED ${OPUS_LIB_SRCS} ${OPUS_LIB_HDRS})

#Add an alias so that library can be used inside the build tree, e.g. when testing
#add_library(OpusCodec::OpusCodec ALIAS OpusCodec)

#Set target properties
target_include_directories(OpusCodec
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

target_compile_features(OpusCodec PRIVATE cxx_auto_type)
target_compile_options(OpusCodec PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall>)

target_link_libraries(OpusCodec
    PUBLIC
        Vpp:Vpp
    PRIVATE
        Opus::opus 
)

##############################################
# Installation instructions

include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/OpusCodec)

install(TARGETS OpusCodec
    EXPORT OpusCodec-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

#This is required so that the exported target has the name OpusCodec and not OpusCodec
set_target_properties(OpusCodec PROPERTIES EXPORT_NAME OpusCodec)

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

#Export the targets to a script
install(EXPORT OpusCodec-targets
  FILE
    OpusCodecTargets.cmake
  NAMESPACE
    OpusCodec::
  DESTINATION
    ${INSTALL_CONFIGDIR}
)

#Create a ConfigVersion.cmake file
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/OpusCodecConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/OpusCodecConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/OpusCodecConfig.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

#Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/OpusCodecConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/OpusCodecConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

##############################################
## Exporting from the build tree

export(EXPORT OpusCodec-targets FILE ${CMAKE_CURRENT_BINARY_DIR}/OpusCodecTargets.cmake NAMESPACE OpusCodec::)

#Register package in user's package registry
export(PACKAGE OpusCodec)

#add_subdirectory(test)
