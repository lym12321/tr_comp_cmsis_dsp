cmake_minimum_required(VERSION 3.22)

# ===== 可配置：根据你的 MCU 调整/缓存 =====
# 以 STM32H7（单精度 FPU）为默认
set(CMSIS_DSP_CPU  "cortex-m7"     CACHE STRING "CPU for CMSIS-DSP (e.g. cortex-m4/m7)")
set(CMSIS_DSP_FPU  "fpv5-sp-d16"   CACHE STRING "FPU for CMSIS-DSP (fpv4-sp-d16/fpv5-sp-d16/fpv5-d16)")
set(CMSIS_DSP_MABI "hard"          CACHE STRING "mfloat-abi (hard/softfp/soft)")
# ARM_MATH_* 宏（H7=ARM_MATH_CM7，F4/G4=ARM_MATH_CM4，F1=ARM_MATH_CM3，M0+=ARM_MATH_CM0PLUS）
set(CMSIS_DSP_ARM_MATH "ARM_MATH_CM7" CACHE STRING "ARM_MATH_* macro for arm_math.h")

set(_CMSIS_DSP_DIR ${CMAKE_CURRENT_LIST_DIR}/CMSIS-DSP)
if(EXISTS "${_CMSIS_DSP_DIR}/CMakeLists.txt")
    add_subdirectory(${_CMSIS_DSP_DIR})
else()
    message(FATAL_ERROR "CMSIS-DSP not found at ${_CMSIS_DSP_DIR}. Did you init the submodule?")
endif()

add_library(cmsis-dsp INTERFACE)
target_link_libraries(CMSISDSP PUBLIC stm32cubemx m)
target_link_libraries(cmsis-dsp INTERFACE CMSISDSP)

target_compile_definitions(cmsis-dsp INTERFACE ${CMSIS_DSP_ARM_MATH})

target_compile_options(cmsis-dsp INTERFACE
        -mthumb
        -mcpu=${CMSIS_DSP_CPU}
        -mfpu=${CMSIS_DSP_FPU}
        -mfloat-abi=${CMSIS_DSP_MABI}
        -O3
        -ffunction-sections
        -fdata-sections
)
target_link_options(cmsis-dsp INTERFACE
        -mthumb
        -mcpu=${CMSIS_DSP_CPU}
        -mfpu=${CMSIS_DSP_FPU}
        -mfloat-abi=${CMSIS_DSP_MABI}
        -Wl,--gc-sections
)

add_library(components::cmsis-dsp ALIAS cmsis-dsp)