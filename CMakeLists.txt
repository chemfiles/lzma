cmake_minimum_required(VERSION 2.8.12)
PROJECT(lzma C)

if (POLICY CMP0063)
    # Use of `<LANG>_VISIBILITY_PRESET` in OBJECT libraries
    cmake_policy(SET CMP0063 NEW)
endif()

include(CheckCSourceCompiles)
include(CheckCCompilerFlag)
include(CheckIncludeFile)
include(CheckSymbolExists)
include(CheckTypeSize)
include(TestBigEndian)

CHECK_C_COMPILER_FLAG("-std=gnu99" COMPILER_SUPPORTS_GNU99)
if(COMPILER_SUPPORTS_GNU99)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")
else()
    CHECK_C_COMPILER_FLAG("-std=c99" COMPILER_SUPPORTS_C99)
    if(COMPILER_SUPPORTS_C99)
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99")
    endif()
endif()

check_c_source_compiles(
  "int test (void *restrict x);\nint main (void) {return 0;}"
  HAVE_RESTRICT)

check_c_source_compiles(
"typedef struct abc *d;\nint test (d __restrict x);\nint main (void) {return 0;}"
  HAVE___RESTRICT)

check_c_source_compiles(
  "static inline int test (void) {return 0;}\nint main (void) {return test();}"
  HAVE_INLINE)

check_c_source_compiles(
  "static __inline int test (void) {return 0;}\nint main (void) {return test();}"
  HAVE___INLINE)

check_include_file(byteswap.h HAVE_BYTESWAP_H)
check_include_file(inttypes.h HAVE_INTTYPES_H)
check_include_file(limits.h HAVE_LIMITS_H)
check_include_file(memory.h HAVE_MEMORY_H)
check_include_file(strings.h HAVE_STRINGS_H)
check_include_file(string.h HAVE_STRING_H)
check_include_file(sys/sysctl.h HAVE_SYS_SYSCTL_H)

check_include_file(stdbool.h HAVE_STDBOOL_H)
if(NOT HAVE_STDBOOL_H)
    check_type_size(_Bool _BOOL)
endif()

check_c_source_compiles(
  "#include<byteswap.h>\nint main(void){bswap_16(0);return 0;}"
  HAVE_BSWAP_16)
check_c_source_compiles(
  "#include<byteswap.h>\nint main(void){bswap_32(0);return 0;}"
  HAVE_BSWAP_32)
check_c_source_compiles(
  "#include<byteswap.h>\nint main(void){bswap_64(0);return 0;}"
  HAVE_BSWAP_64)

test_big_endian(WORDS_BIGENDIAN)

set(HAVE_CHECK_CRC64 1)
set(HAVE_CHECK_SHA256 1)

set(HAVE_DECODER_ARM 1)
set(HAVE_DECODER_ARMTHUMB 1)
set(HAVE_DECODER_DELTA 1)
set(HAVE_DECODER_IA64 1)
set(HAVE_DECODER_LZMA1 1)
set(HAVE_DECODER_LZMA2 1)
set(HAVE_DECODER_POWERPC 1)
set(HAVE_DECODER_SPARC 1)
set(HAVE_DECODER_X86 1)

set(HAVE_ENCODER_ARM 1)
set(HAVE_ENCODER_ARMTHUMB 1)
set(HAVE_ENCODER_DELTA 1)
set(HAVE_ENCODER_IA64 1)
set(HAVE_ENCODER_LZMA1 1)
set(HAVE_ENCODER_LZMA2 1)
set(HAVE_ENCODER_POWERPC 1)
set(HAVE_ENCODER_SPARC 1)
set(HAVE_ENCODER_X86 1)

set(HAVE_MF_BT2 1)
set(HAVE_MF_BT3 1)
set(HAVE_MF_BT4 1)
set(HAVE_MF_HC3 1)
set(HAVE_MF_HC4 1)

check_type_size(int16_t INT16_T)
check_type_size(int32_t INT32_T)
check_type_size(int64_t INT64_T)
check_type_size(intmax_t INTMAX_T)
check_type_size(uint8_t UINT8_T)
check_type_size(uint16_t UINT16_T)
check_type_size(uint32_t UINT32_T)
check_type_size(uint64_t UINT64_T)
check_type_size(uintmax_t UINTMAX_T)

check_type_size("short" SIZE_OF_SHORT)
check_type_size("int" SIZE_OF_INT)
check_type_size("long" SIZE_OF_LONG)
check_type_size("long long" SIZE_OF_LONG_LONG)

check_type_size("unsigned short" SIZE_OF_UNSIGNED_SHORT)
check_type_size("unsigned" SIZE_OF_UNSIGNED)
check_type_size("unsigned long" SIZE_OF_UNSIGNED_LONG)
check_type_size("unsigned long long" SIZE_OF_UNSIGNED_LONG_LONG)
check_type_size("size_t" SIZE_OF_SIZE_T)

check_type_size("__int64" __INT64)
check_type_size("unsigned __int64" UNSIGNED___INT64)

check_type_size(uintptr_t UINTPTR_T)
if(NOT HAVE_UINTPTR_T)
    if("${CMAKE_SIZEOF_VOID_P}" EQUAL 8)
        set(uintptr_t "uint64_t")
    else()
        set(uintptr_t "uint32_t")
    endif()
endif()

set(LZMA_SRCS
    common/sysdefs.h
    common/tuklib_integer.h
    liblzma/check/check.c
    liblzma/check/crc32_fast.c
    liblzma/check/crc32_table.c
    liblzma/check/crc64_fast.c
    liblzma/check/crc64_table.c
    liblzma/check/sha256.c
    liblzma/common/alone_decoder.c
    liblzma/common/alone_encoder.c
    liblzma/common/auto_decoder.c
    liblzma/common/block_buffer_decoder.c
    liblzma/common/block_buffer_encoder.c
    liblzma/common/block_decoder.c
    liblzma/common/block_encoder.c
    liblzma/common/block_header_decoder.c
    liblzma/common/block_header_encoder.c
    liblzma/common/block_util.c
    liblzma/common/common.c
    liblzma/common/easy_buffer_encoder.c
    liblzma/common/easy_decoder_memusage.c
    liblzma/common/easy_encoder.c
    liblzma/common/easy_encoder_memusage.c
    liblzma/common/easy_preset.c
    liblzma/common/filter_buffer_decoder.c
    liblzma/common/filter_buffer_encoder.c
    liblzma/common/filter_common.c
    liblzma/common/filter_decoder.c
    liblzma/common/filter_encoder.c
    liblzma/common/filter_flags_decoder.c
    liblzma/common/filter_flags_encoder.c
    liblzma/common/index.c
    liblzma/common/index_decoder.c
    liblzma/common/index_encoder.c
    liblzma/common/index_hash.c
    liblzma/common/stream_buffer_decoder.c
    liblzma/common/stream_buffer_encoder.c
    liblzma/common/stream_decoder.c
    liblzma/common/stream_encoder.c
    liblzma/common/stream_flags_common.c
    liblzma/common/stream_flags_decoder.c
    liblzma/common/stream_flags_encoder.c
    liblzma/common/vli_decoder.c
    liblzma/common/vli_encoder.c
    liblzma/common/vli_size.c
    liblzma/delta/delta_common.c
    liblzma/delta/delta_decoder.c
    liblzma/delta/delta_encoder.c
    liblzma/lz/lz_decoder.c
    liblzma/lz/lz_encoder.c
    liblzma/lz/lz_encoder_mf.c
    liblzma/lzma/fastpos_table.c
    liblzma/lzma/lzma2_decoder.c
    liblzma/lzma/lzma2_encoder.c
    liblzma/lzma/lzma_decoder.c
    liblzma/lzma/lzma_encoder.c
    liblzma/lzma/lzma_encoder_optimum_fast.c
    liblzma/lzma/lzma_encoder_optimum_normal.c
    liblzma/lzma/lzma_encoder_presets.c
    liblzma/rangecoder/price_table.c
    liblzma/simple/arm.c
    liblzma/simple/armthumb.c
    liblzma/simple/ia64.c
    liblzma/simple/powerpc.c
    liblzma/simple/simple_coder.c
    liblzma/simple/simple_decoder.c
    liblzma/simple/simple_encoder.c
    liblzma/simple/sparc.c
    liblzma/simple/x86.c
)

configure_file(config.h.in config.h @ONLY)

add_library(lzma OBJECT ${LZMA_SRCS})
target_compile_definitions(lzma PUBLIC "-DHAVE_CONFIG_H")
target_include_directories(lzma PUBLIC
    "${CMAKE_CURRENT_SOURCE_DIR}/common"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/api"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/check"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/common"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/delta"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/lz"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/lzma"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/rangecoder"
    "${CMAKE_CURRENT_SOURCE_DIR}/liblzma/simple"
    "${CMAKE_CURRENT_BINARY_DIR}"
)
