CMAKE_MINIMUM_REQUIRED(VERSION 2.8.3)

PROJECT(libflac)

# Required modules
include(GNUInstallDirs)
include(CheckIncludeFiles)


SET(SRC_LIBFLAC_PATH ${CMAKE_CURRENT_SOURCE_DIR}/src/libFLAC/)
SET(HDR_LIBFLAC_PATH ${CMAKE_CURRENT_SOURCE_DIR}/include/FLAC/)
SET(SRC_UTF8_PATH ${CMAKE_CURRENT_SOURCE_DIR}/src/share/utf8/)

SET(utf8_SRC
	${SRC_UTF8_PATH}/charset.c
	${SRC_UTF8_PATH}/iconvert.c
	${SRC_UTF8_PATH}/utf8.c
	)

IF(MSVC)
	LIST(APPEND utf8_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/share/win_utf8_io/win_utf8_io.c)
ENDIF(MSVC)
	
SET(libflac_SRC
	${SRC_LIBFLAC_PATH}/bitmath.c
	${SRC_LIBFLAC_PATH}/bitreader.c
	${SRC_LIBFLAC_PATH}/bitwriter.c
	${SRC_LIBFLAC_PATH}/cpu.c
	${SRC_LIBFLAC_PATH}/crc.c
	${SRC_LIBFLAC_PATH}/fixed.c
	${SRC_LIBFLAC_PATH}/fixed_intrin_sse2.c
	${SRC_LIBFLAC_PATH}/fixed_intrin_ssse3.c
	${SRC_LIBFLAC_PATH}/float.c
	${SRC_LIBFLAC_PATH}/format.c
	${SRC_LIBFLAC_PATH}/lpc.c
	${SRC_LIBFLAC_PATH}/lpc_intrin_avx2.c
	${SRC_LIBFLAC_PATH}/lpc_intrin_sse.c
	${SRC_LIBFLAC_PATH}/lpc_intrin_sse2.c
	${SRC_LIBFLAC_PATH}/lpc_intrin_sse41.c
	${SRC_LIBFLAC_PATH}/md5.c
	${SRC_LIBFLAC_PATH}/memory.c
	${SRC_LIBFLAC_PATH}/metadata_iterators.c
	${SRC_LIBFLAC_PATH}/metadata_object.c
	${SRC_LIBFLAC_PATH}/ogg_decoder_aspect.c
	${SRC_LIBFLAC_PATH}/ogg_encoder_aspect.c
	${SRC_LIBFLAC_PATH}/ogg_helper.c
	${SRC_LIBFLAC_PATH}/ogg_mapping.c
	${SRC_LIBFLAC_PATH}/stream_decoder.c
	${SRC_LIBFLAC_PATH}/stream_encoder.c
	${SRC_LIBFLAC_PATH}/stream_encoder_framing.c
	${SRC_LIBFLAC_PATH}/stream_encoder_intrin_avx2.c
	${SRC_LIBFLAC_PATH}/stream_encoder_intrin_sse2.c
	${SRC_LIBFLAC_PATH}/stream_encoder_intrin_ssse3.c
	${SRC_LIBFLAC_PATH}/window.c
)

SET(libflac_HDR
	${HDR_LIBFLAC_PATH}/all.h
	${HDR_LIBFLAC_PATH}/assert.h
	${HDR_LIBFLAC_PATH}/callback.h
	${HDR_LIBFLAC_PATH}/export.h
	${HDR_LIBFLAC_PATH}/format.h
	${HDR_LIBFLAC_PATH}/metadata.h
	${HDR_LIBFLAC_PATH}/ordinals.h
	${HDR_LIBFLAC_PATH}/stream_decoder.h
	${HDR_LIBFLAC_PATH}/stream_encoder.h
)

ADD_DEFINITIONS(
	-DFLAC_API_EXPORTS 
	-DFLACPP_API_EXPORTS
	-D_LIB
	-DFLAC__HAS_OGG
	-DFLAC__CPU_IA32
	-DFLAC__SSE_OS
	-DFLAC__NO_NASM
	-DFLAC__HAS_X86INTRIN
	-DFLAC__ALIGN_MALLOC_DATA
	-DVERSION="1.3.1"
	-DFLAC__NO_DLL
	-DFLAC__OVERFLOW_DETECT
)

if(BUILD_FRAMEWORK)
    set(BUILD_SHARED_LIBS TRUE)
endif()

SET(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
#MESSAGE(STATUS "OGGDIR: ${OGGDIR}")
FIND_PACKAGE(ogg REQUIRED)

INCLUDE_DIRECTORIES(${OGG_INCLUDE_DIRS})
INCLUDE_DIRECTORIES(include)

INCLUDE_DIRECTORIES(${SRC_LIBFLAC_PATH}/include)
ADD_LIBRARY(flac ${libflac_SRC} ${libflac_HDR} ${utf8_SRC})

INSTALL(TARGETS flac 
	RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	FRAMEWORK DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

INSTALL(FILES ${libflac_HDR} DESTINATION ${CMAKE_INSTALL_FULL_INCLUDEDIR}/flac)