cmake_minimum_required(VERSION 3.13)

project(freertos_examples)

# add_executable(afr_workshop src/main.c)
file(GLOB SOURCES "src/*.c")
add_executable(afr_workshop ${SOURCES})

# Add workshop code and files.
include_directories(afr_workshop PRIVATE include)

# Tell IDF build to link against this target.
set(IDF_PROJECT_EXECUTABLE afr_workshop)

# Add some extra components. IDF_EXTRA_COMPONENT_DIRS is an variable used by ESP-IDF
# to collect extra components.
get_filename_component(
    EXTRA_COMPONENT_DIRS
    "components/foo" ABSOLUTE
)
list(APPEND IDF_EXTRA_COMPONENT_DIRS ${EXTRA_COMPONENT_DIRS})
get_filename_component(
    EXTRA_COMPONENT_DIRS
    "components/afr-m5stickc-bsp" ABSOLUTE
)
list(APPEND IDF_EXTRA_COMPONENT_DIRS ${EXTRA_COMPONENT_DIRS})

# As of now there's no offical way to redefine config files outside of Amazon FreeRTOS source tree.
# This is a temporary approach to inject an include path so that this takes precedence over the
# config file directory inside Amazon FreeRTOS.
include_directories(BEFORE amazon-freertos-configs)

# Add amazon freertos as an subdirectory. AFR_BOARD tells which board to target.
set(AFR_BOARD espressif.esp32_devkitc CACHE INTERNAL "")
add_subdirectory(amazon-freertos)

# Link against the mqtt demo so that we can use it. Dependencies of this demo are transitively
# linked.
target_link_libraries(afr_workshop 
    PRIVATE 
        AFR::demo_ble_numeric_comparison
        AFR::demo_mqtt
        AFR::shadow
)


