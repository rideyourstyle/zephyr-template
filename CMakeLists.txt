# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.20.0)

find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})
project(paramo-host)


cmake_path(SET PARAMO_HOST_BASE NORMALIZE ${CMAKE_CURRENT_SOURCE_DIR}/../..)

cmake_path(SET MAIN_APP_BASE NORMALIZE ${PARAMO_HOST_BASE}/Source/MainApp)
cmake_path(SET LIBRARY_ZEPHYR_BASE NORMALIZE ${PARAMO_HOST_BASE}/LibraryZephyr)

# tinyUSB
cmake_path(SET TINYUSB_BASE NORMALIZE ${LIBRARY_ZEPHYR_BASE}/tinyUSB)
set(TINYUSB_SRCS
        ${TINYUSB_BASE}/src/tusb.c
        ${TINYUSB_BASE}/src/common/tusb_fifo.c
        ${TINYUSB_BASE}/src/device/usbd.c
        ${TINYUSB_BASE}/src/device/usbd_control.c
        # used HID class
        ${TINYUSB_BASE}/src/class/cdc/cdc_device.c
)
set(TINYUSB_INC
        ${TINYUSB_BASE}/src
        ${TINYUSB_BASE}/src/common
        ${TINYUSB_BASE}/src/device
        # used HID class
        ${TINYUSB_BASE}/src/class/cdc
)

target_compile_definitions(app PRIVATE
        -DDIGITAL_CYLINDER
        -DCFG_TUSB_OS=OPT_OS_ZEPHYR
)

add_subdirectory(${MAIN_APP_BASE}/Driver/Crc {CMAKE_CURRENT_BINARY_DIR}-tinyUSB)

target_sources(app PRIVATE
        ${MAIN_APP_BASE}/Main.cpp

        ${PARAMO_HOST_BASE}/Deps/DmFramework/Firmware/Library/Source/Utilities/Stub/CustomAssert.cpp
        ${PARAMO_HOST_BASE}/Deps/DmFramework/Firmware/Library/Source/Utilities/Stub/Logger.cpp

        ${MAIN_APP_BASE}/Driver/Crc/Stub/CrcHardware.cpp
        ${MAIN_APP_BASE}/Driver/Crc/CrcManager.cpp
        ${MAIN_APP_BASE}/Driver/Crc/CrcSoftware.cpp

        ${MAIN_APP_BASE}/Driver/Usb/Stub/TinyUsbStack.cpp
        ${MAIN_APP_BASE}/Driver/Usb/Stub/UsbDescriptors.cpp

        ${MAIN_APP_BASE}/Sys/OsWrapper/Implementation/Stub/OsTaskRtos.cpp
)

include_directories(app PRIVATE
        ${PARAMO_HOST_BASE}/Config
        ${PARAMO_HOST_BASE}/Config/Common

        ${PARAMO_HOST_BASE}/Deps/DmFramework/Firmware/Library/Source
        ${PARAMO_HOST_BASE}/Deps/DmFramework/Firmware/CustomCode/CcLibrary/Source/Commands

        ${PARAMO_HOST_BASE}/Library
        #${PARAMO_HOST_BASE}/Library/CMSIS/Device/ST/STM32U5xx/Include
        #${PARAMO_HOST_BASE}/Library/CMSIS/Include

        #${PARAMO_HOST_BASE}/Library/FreeRTOS/FreeRTOS-Kernel/config
        #${PARAMO_HOST_BASE}/Library/FreeRTOS/FreeRTOS-Kernel/include

        ${PARAMO_HOST_BASE}/Library/TinyCBOR
        #${PARAMO_HOST_BASE}/Library/TinyUSB
        #${PARAMO_HOST_BASE}/Library/TinyUSB/src
        #${PARAMO_HOST_BASE}/Library/STM32U5xx_HAL_Driver/Inc

        ${MAIN_APP_BASE}/Application
        ${MAIN_APP_BASE}/Application/Features
        ${MAIN_APP_BASE}/Application/Media
        ${MAIN_APP_BASE}/Application/Usb
        ${MAIN_APP_BASE}/Application/Utility

        ${MAIN_APP_BASE}/Driver/BlockDevice
        ${MAIN_APP_BASE}/Driver/Crc
        ${MAIN_APP_BASE}/Driver/Crc/Stub
        ${MAIN_APP_BASE}/Driver/Led
        ${MAIN_APP_BASE}/Driver/Motor
        ${MAIN_APP_BASE}/Driver/Rtc
        ${MAIN_APP_BASE}/Driver/SpiCommunication
        ${MAIN_APP_BASE}/Driver/SpiCommunication/BaseSpi
        ${MAIN_APP_BASE}/Driver/SpiCommunication/LegicSe
        ${MAIN_APP_BASE}/Driver/SpiCommunication/Zigbee
        ${MAIN_APP_BASE}/Driver/SpiFlash
        ${MAIN_APP_BASE}/Driver/System
        ${MAIN_APP_BASE}/Driver/Timer
        ${MAIN_APP_BASE}/Driver/Usb
        ${MAIN_APP_BASE}/Driver/Usb/Stub

        ${MAIN_APP_BASE}/Library

        ${MAIN_APP_BASE}/Middleware/Bolt
        ${MAIN_APP_BASE}/Middleware/Communication
        ${MAIN_APP_BASE}/Middleware/Db
        ${MAIN_APP_BASE}/Middleware/Db/Types
        ${MAIN_APP_BASE}/Middleware/Logging
        ${MAIN_APP_BASE}/Middleware/MediaStack
        ${MAIN_APP_BASE}/Middleware/MediaStack/Commands
        ${MAIN_APP_BASE}/Middleware/MediaStack/Media
        ${MAIN_APP_BASE}/Middleware/Power
        ${MAIN_APP_BASE}/Middleware/Signalization
        ${MAIN_APP_BASE}/Middleware/Storage
        ${MAIN_APP_BASE}/Middleware/Storage/Cbor
        ${MAIN_APP_BASE}/Middleware/Storage/HashTable
        ${MAIN_APP_BASE}/Middleware/Storage/Stub
        ${MAIN_APP_BASE}/Middleware/Synchronizer
        ${MAIN_APP_BASE}/Middleware/Time
        ${MAIN_APP_BASE}/Middleware/UsbProtocol
        ${MAIN_APP_BASE}/Middleware/Zigbee

        ${MAIN_APP_BASE}/Sys
        ${MAIN_APP_BASE}/Sys/BlockPool
        ${MAIN_APP_BASE}/Sys/Crypto
        ${MAIN_APP_BASE}/Sys/Crypto/Wrapper
        ${MAIN_APP_BASE}/Sys/Error
        ${MAIN_APP_BASE}/Sys/HwTimer
        ${MAIN_APP_BASE}/Sys/Itc
        ${MAIN_APP_BASE}/Sys/ItcMessage
        ${MAIN_APP_BASE}/Sys/OsWrapper
        ${MAIN_APP_BASE}/Sys/OsWrapper/Implementation
        ${MAIN_APP_BASE}/Sys/Utility

        # tinyUSB
        #${PARAMO_HOST_BASE}/Project/Zephyr  # tusb_config.h, needed for the latest
        #${TINYUSB_INC}
)

target_link_libraries(app INTERFACE
        CrcManager
)
