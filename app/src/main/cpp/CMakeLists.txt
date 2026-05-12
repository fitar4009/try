cmake_minimum_required(VERSION 3.22.1)
project("kala_arichta_whisper")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ─── whisper.cpp source files ───────────────────────────────────────────────
# These files must be placed in app/src/main/cpp/whisper/ by the developer.
# See SETUP.md for instructions on obtaining whisper.cpp.

set(WHISPER_DIR ${CMAKE_CURRENT_SOURCE_DIR}/whisper)

add_library(
    whisper
    STATIC
    ${WHISPER_DIR}/ggml.c
    ${WHISPER_DIR}/ggml-alloc.c
    ${WHISPER_DIR}/ggml-backend.c
    ${WHISPER_DIR}/ggml-quants.c
    ${WHISPER_DIR}/whisper.cpp
)

target_include_directories(whisper PUBLIC ${WHISPER_DIR})

target_compile_options(whisper PRIVATE
    -O3
    -DGGML_USE_CPU
    -ffast-math
)

# ─── JNI bridge ─────────────────────────────────────────────────────────────
add_library(
    kala_whisper_jni
    SHARED
    whisper_jni.cpp
)

target_include_directories(kala_whisper_jni PRIVATE ${WHISPER_DIR})

target_link_libraries(
    kala_whisper_jni
    whisper
    android
    log
    OpenSLES
)

target_compile_options(kala_whisper_jni PRIVATE
    -O3
    -std=c++17
)
