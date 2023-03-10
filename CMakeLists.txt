cmake_minimum_required(VERSION 3.12)

set (THE_PROJECT_NAME "Pi_the_Robot")  # set project name

file(TO_CMAKE_PATH "$ENV{PICO_SDK_PATH}" PICO_SDK_PATH_CMAKE)

# initialize the SDK based on PICO_SDK_PATH
include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)
include($ENV{PICO_SDK_PATH}/tools/CMakeLists.txt)

# project(Pico_FreeRTOS_template C CXX ASM)
project(${THE_PROJECT_NAME} C CXX ASM)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# initialize the Pico SDK
pico_sdk_init()

# add gcc compiler options

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fshort-enums -Wunused-result ")

# collect all source .c files
FILE(GLOB Pico_source_files ${CMAKE_SOURCE_DIR}/src/*.c)

add_executable(${PROJECT_NAME}
    ${Pico_source_files}
)

# generate the header file into the source tree as it is included in the RP2040 datasheet
# pico_generate_pio_header(${PROJECT_NAME} 
#   ${CMAKE_CURRENT_LIST_DIR}/src/ws2812.pio OUTPUT_DIR ${CMAKE_CURRENT_LIST_DIR}/generated)

FILE(GLOB FreeRTOS_src ${CMAKE_SOURCE_DIR}/lib/FreeRTOS-Kernel/*.c)

add_library( FreeRTOS STATIC
    ${FreeRTOS_src}
    ${CMAKE_SOURCE_DIR}/lib/FreeRTOS-Kernel/portable/GCC/ARM_CM0/port.c
    ${CMAKE_SOURCE_DIR}/lib/FreeRTOS-Kernel/portable/MemMang/heap_1.c    # heap_4.c
)

target_include_directories( FreeRTOS PUBLIC
    ${CMAKE_SOURCE_DIR}/lib/FreeRTOS-Kernel/include
    ${CMAKE_SOURCE_DIR}/lib/FreeRTOS-Kernel/portable/GCC/ARM_CM0
    ${CMAKE_SOURCE_DIR}/include
)

target_include_directories ( ${PROJECT_NAME} PUBLIC
    ${CMAKE_SOURCE_DIR}/include
)

target_link_libraries( ${PROJECT_NAME} 
    pico_stdlib 
    pico_multicore
    hardware_gpio 
    hardware_i2c
    hardware_spi
    hardware_pwm
    hardware_adc
    hardware_pio
    hardware_dma
    FreeRTOS
    )

pico_add_extra_outputs(${PROJECT_NAME})

# Enable usb output, disable uart output
pico_enable_stdio_usb(${PROJECT_NAME} 0)
pico_enable_stdio_uart(${PROJECT_NAME} 1)

# print executable size
add_custom_command(TARGET ${PROJECT_NAME}
    POST_BUILD
    COMMAND date.exe /t >> ${CMAKE_SOURCE_DIR}/File_size.txt
    COMMAND arm-none-eabi-size.exe -G ${THE_PROJECT_NAME}.elf
    COMMAND arm-none-eabi-size.exe -G ${THE_PROJECT_NAME}.elf  >> ${CMAKE_SOURCE_DIR}/File_size.txt
    VERBATIM
)