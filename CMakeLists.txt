cmake_minimum_required(VERSION 3.22)

add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/CMSIS-DSP)

add_library(cmsis-dsp INTERFACE)
target_link_libraries(CMSISDSP PUBLIC stm32cubemx m)
target_link_libraries(cmsis-dsp INTERFACE CMSISDSP)

add_library(components::cmsis-dsp ALIAS cmsis-dsp)