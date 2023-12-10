cmake_minimum_required(VERSION 3.12)

set(PROJECT rp2040-example)
set(PROJECT_URL "https://github.com/tomas-pecserke/rp2040-example")
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(PICO_SDK_MIN_VERSION "1.5.1")

# Pull in SDK (must be before project)
include(pico_sdk_import.cmake)
include(pico_extras_import_optional.cmake)

project(${PROJECT} VERSION 1.0.0 LANGUAGES C CXX ASM)

if (PICO_SDK_VERSION_STRING VERSION_LESS ${PICO_SDK_MIN_VERSION})
    message(FATAL_ERROR "Raspberry Pi Pico SDK version ${PICO_SDK_MIN_VERSION} (or later) required. Your version is ${PICO_SDK_VERSION_STRING}")
endif()

# Initialize the SDK
pico_sdk_init()
include(example_auto_set_url.cmake)

add_compile_options(
    -Wall
    -Wno-format          # int != int32_t as far as the compiler is concerned because gcc has int32_t as long int
    -Wno-unused-function # we have some for the docs that aren't called
)
if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
    add_compile_options(-Wno-maybe-uninitialized)
endif()

add_executable(${PROJECT} src/main.cpp)
target_include_directories(${PROJECT} PUBLIC src include)
target_sources(${PROJECT} PUBLIC )

# pull in common dependencies
target_link_libraries(${PROJECT} pico_stdlib)

# enable usb output, disable uart output
pico_enable_stdio_usb(${PROJECT} 1)
pico_enable_stdio_uart(${PROJECT} 0)

# create map/bin/hex/uf2 file etc.
pico_add_extra_outputs(${PROJECT})

# add version via pico_set_program_url
pico_set_program_version(${PROJECT} "${PROJECT_VERSION}")

# add url via pico_set_program_url
pico_set_program_url(${PROJECT} ${PROJECT_URL})
