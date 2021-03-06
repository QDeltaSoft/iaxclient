# CMakeLists.txt, for utils/iaxclient/lib - 20131030
# even if we don't select the codec, speex is used for pre-processing audio
set(ENABLE_SPEEX 1)
set (PROJECT_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})


set(IAXCLIENT_BASE_SOURCES
    audio_encode.c
    audio_file.c
    audio_pulseaudio.c
    codec_alaw.c
    codec_gsm.c
    codec_ulaw.c
    iaxclient_lib.c
)

set(LIBIAX2_SOURCES
    libiax2/src/iax.c
    libiax2/src/iax2-parser.c
    libiax2/src/jitterbuf.c
    libiax2/src/md5.c
)

set(GSM_SOURCES
    gsm/src/add.c
    gsm/src/code.c
    gsm/src/debug.c
    gsm/src/decode.c
    gsm/src/gsm_create.c
    gsm/src/gsm_decode.c
    gsm/src/gsm_destroy.c
    gsm/src/gsm_encode.c
    gsm/src/gsm_explode.c
    gsm/src/gsm_implode.c
    gsm/src/gsm_option.c
    gsm/src/gsm_print.c
    gsm/src/long_term.c
    gsm/src/lpc.c
    gsm/src/preprocess.c
    gsm/src/rpe.c
    gsm/src/short_term.c
    gsm/src/table.c
)

if (WIN32)
    list(APPEND IAXCLIENT_BASE_SOURCES winfuncs.c)
else()
    list(APPEND IAXCLIENT_BASE_SOURCES unixfuncs.c)
endif(WIN32)

if (ENABLE_SPEEX)
    list(APPEND IAXCLIENT_BASE_SOURCES codec_speex.c)
    
    set(SPEEX_SOURCES 
        libspeex/bits.c
        libspeex/cb_search.c
        libspeex/exc_10_16_table.c
        libspeex/exc_10_32_table.c
        libspeex/exc_20_32_table.c
        libspeex/exc_5_256_table.c
        libspeex/exc_5_64_table.c
        libspeex/exc_8_128_table.c
        libspeex/filters.c
        libspeex/gain_table.c
        libspeex/gain_table_lbr.c
        libspeex/hexc_10_32_table.c
        libspeex/hexc_table.c
        libspeex/high_lsp_tables.c
        libspeex/jitter.c
        libspeex/lbr_48k_tables.c
        libspeex/lpc.c
        libspeex/lsp.c
        libspeex/lsp_tables_nb.c
        libspeex/ltp.c
        libspeex/math_approx.c
        libspeex/mdf.c
        libspeex/medfilter.c
        libspeex/misc.c
        libspeex/modes.c
        libspeex/nb_celp.c
        libspeex/preprocess.c
        libspeex/quant_lsp.c
        libspeex/sb_celp.c
        libspeex/smallft.c
        libspeex/speex.c
        libspeex/speex_callbacks.c
        libspeex/speex_header.c
        libspeex/stereo.c
        libspeex/vbr.c
        libspeex/vq.c
        )
    
    include_directories(${PROJECT_SOURCE_DIR}/libspeex/include)
endif(ENABLE_SPEEX)

if (ENABLE_ALSA)
    list(APPEND IAXCLIENT_BASE_SOURCES audio_alsa.c)
endif(ENABLE_ALSA)

if (ENABLE_PORTAUDIO)
    list(APPEND IAXCLIENT_BASE_SOURCES audio_portaudio.c)
endif(ENABLE_PORTAUDIO)

if (APPLE)
    add_definitions(-DMACOSX)
endif(APPLE)

list(APPEND IAXCLIENT_BASE_SOURCES spandsp/plc.c)

add_definitions(-DAUDIO_PULSEAUDIO=1)
add_definitions(-DLIBIAX)

# for GSM
add_definitions(-DHAS_STRING_H -DHAS_STDLIB_H)
add_definitions(-DCODEC_GSM)

if (MSVC)
    # turn off various warnings
    set(WFLAGS)
    foreach(warning 4244 4305 4996)
        set(WFLAGS "${WFLAGS} /wd${warning}")
    endforeach()
    add_definitions(${WFLAGS})
endif ()

include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/libiax2/src)
include_directories(${PROJECT_SOURCE_DIR}/gsm/inc)

add_library(iaxclient_lib STATIC 
    ${IAXCLIENT_BASE_SOURCES} 
    ${GSM_SOURCES} 
    ${SPEEX_SOURCES}
    ${LIBIAX2_SOURCES})

target_link_libraries(iaxclient_lib ${CMAKE_THREAD_LIBS_INIT} pulse-simple)

# eof
