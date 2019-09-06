# CMakeLists.txt in Opus dir

# SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_BUILD_FOR_SHORT")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_CRT_SECURE_NO_WARNINGS")


SET (OPUS_INCLUDE_DIRS 
	${PROJECT_SOURCE_DIR}/externals/CodecUtils/include/CodecUtils
)

SET (OPUS_INCLUDE_DEPENDENCIES 
	${PROJECT_SOURCE_DIR}/include/OpusCodec/
	${PROJECT_SOURCE_DIR}/externals/vcpkg/installed/x86-windows/include/
	${PROJECT_SOURCE_DIR}/externals/vcpkg/installed/x86-windows/include/opus
)

#INCLUDE_DIRECTORIES(
#	${artist_SOURCE_DIR}/src/libs/CodecUtils
#	$ENV{OPUS_ROOT}/include
#	$ENV{OPUS_ROOT}/celt
#	$ENV{OPUS_ROOT}/silk
#	$ENV{OPUS_ROOT}/silk/fixed
#	$ENV{OPUS_ROOT}/silk/float
#)

# Lib directories
#LINK_DIRECTORIES(
#	${artistLink}
#)

SET(OPUS_LIB_HDRS
	../include/OpusCodec/OpusCodec.h
	../include/OpusCodec/OpusFactory.h
	StdAfx.h
	targetver.h
)

SET(OPUS_LIB_SRCS 
	OpusCodec.cpp
	OpusFactory.cpp
	dllmain.cpp
	StdAfx.cpp
)

ADD_LIBRARY( OpusCodec SHARED ${OPUS_LIB_SRCS} ${OPUS_LIB_HDRS})
install(TARGETS OpusCodec
    EXPORT OpusCodecTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
export(TARGETS
        OpusCodec
    NAMESPACE OpusCodec::
    FILE "${CMAKE_CURRENT_BINARY_DIR}/OpusCodecConfig.cmake"
)
install(EXPORT OpusCodecTargets
  FILE
    OpusCodecTargets.cmake
  NAMESPACE
    OpusCodec::
  DESTINATION
    ${CMAKE_INSTALL_LIBDIR}/cmake/OpusCodec
)

target_include_directories(OpusCodec
    PUBLIC 
        $<INSTALL_INTERFACE:>    
        $<BUILD_INTERFACE:${OPUS_INCLUDE_DIRS}>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}
		${OPUS_INCLUDE_DEPENDENCIES}
)

target_compile_features(OpusCodec PRIVATE cxx_std_11)


TARGET_LINK_LIBRARIES(
	OpusCodec
	Opus::opus
#	opus
#	celt
#	silk_common
#	silk_fixed
#	silk_float
) 
