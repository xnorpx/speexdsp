cmake_minimum_required(VERSION 3.0.2)
project(speexdsp LANGUAGES C)

include(CheckTypeSize)
check_type_size("int16_t" SIZEOF_INT16)

set(SPEEXDSP_FOLDER ${CMAKE_CURRENT_SOURCE_DIR})
set(SPEEXDSP_SOURCE ${SPEEXDSP_FOLDER}/libspeexdsp)

set(SIZE16 "int16_t")
set(USIZE16 "uint16_t")
set(SIZE32 "int32_t")
set(USIZE32 "uint32_t")
set(EXPORT "__attribute__((visibility(\"default\")))")
set(FLOATING_POINT 1)
set(USE_SMALLFT 1)

if(HAVE_SYS_TYPES_H)
    set(INCLUDE_STDINT "#include <sys/types.h>")
endif()
if(HAVE_STDINT_H)
    set(INCLUDE_STDINT "#include <stdint.h>")
endif()
if(HAVE_STDDEF_H)
    set(INCLUDE_STDINT "#include <inttypes.h>")
endif()

if(NOT WIN32)
    include(CheckLibraryExists)
    check_library_exists(m floor "" HAVE_LIBM)
    if(HAVE_LIBM)
        list(APPEND SPEEXDSP_REQUIRED_LIBRARIES m)
    endif()
endif()

#configure_file(
#        ${SPEEXDSP_FOLDER}/include/speex/speexdsp_config_types.h.in
#        ${SPEEXDSP_FOLDER}/include/speex/speexdsp_config_types.h
#)

if(WIN32)
  add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

configure_file(config.h.cmake ${SPEEXDSP_FOLDER}/config.h)

add_library(speexdsp ${SPEEXDSP_SOURCE}/resample.c
        ${SPEEXDSP_SOURCE}/buffer.c
        ${SPEEXDSP_SOURCE}/fftwrap.c
        ${SPEEXDSP_SOURCE}/filterbank.c
        ${SPEEXDSP_SOURCE}/jitter.c
        ${SPEEXDSP_SOURCE}/kiss_fft.c
        ${SPEEXDSP_SOURCE}/kiss_fftr.c
        ${SPEEXDSP_SOURCE}/mdf.c
        ${SPEEXDSP_SOURCE}/preprocess.c
        ${SPEEXDSP_SOURCE}/scal.c
        ${SPEEXDSP_SOURCE}/smallft.c)

target_include_directories(speexdsp PUBLIC ${SPEEXDSP_FOLDER}/include PRIVATE ${SPEEXDSP_FOLDER} ${SPEEXDSP_SOURCE} ${SPEEXDSP_FOLDER}/include ${SPEEXDSP_FOLDER}/include/speex)
target_compile_options(speexdsp PRIVATE "-DHAVE_CONFIG_H")
target_link_libraries(speexdsp PRIVATE ${SPEEXDSP_REQUIRED_LIBRARIES})

target_link_options(speexdsp PRIVATE
        -sEXPORTED_FUNCTIONS=['_speex_echo_state_init'])

#if (NOT BUILD_SHARED_LIBS)
#    add_executable(test_denoise ${SPEEXDSP_SOURCE}/testdenoise.c)
#    target_include_directories(test_denoise PRIVATE ${SPEEXDSP_SOURCE})
#    target_link_libraries(test_denoise PRIVATE speexdsp)
#
#    add_executable(test_echo ${SPEEXDSP_SOURCE}/testecho.c)
#    target_include_directories(test_echo PRIVATE ${SPEEXDSP_SOURCE})
#    target_link_libraries(test_echo PRIVATE speexdsp)
#
#    add_executable(test_jitter ${SPEEXDSP_SOURCE}/testjitter.c)
#    target_include_directories(test_jitter PRIVATE ${SPEEXDSP_SOURCE})
#    target_link_libraries(test_jitter PRIVATE speexdsp)
#
#    add_executable(test_resample ${SPEEXDSP_SOURCE}/testresample.c)
#    target_include_directories(test_resample PRIVATE ${SPEEXDSP_SOURCE})
#    target_link_libraries(test_resample PRIVATE speexdsp)
#
#    add_executable(test_resample2 ${SPEEXDSP_SOURCE}/testresample2.c)
#    target_include_directories(test_resample2 PRIVATE ${SPEEXDSP_SOURCE})
#    target_link_libraries(test_resample2 PRIVATE speexdsp)
#endif()