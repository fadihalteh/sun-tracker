cmake_minimum_required(VERSION 3.16)
project(SolarStewartTracker LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(MSVC)
    add_compile_options(/W4)
else()
    add_compile_options(-Wall -Wextra -Wpedantic)
    if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        add_compile_options(-Wno-free-nonheap-object)
    endif()
endif()

option(SOLAR_TRY_OPENCV "Enable OpenCV viewer if OpenCV is found" ON)
option(SOLAR_ENABLE_COVERAGE "Enable coverage instrumentation" OFF)

function(solar_enable_coverage target_name)
    if(NOT SOLAR_ENABLE_COVERAGE)
        return()
    endif()
    if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
        target_compile_options(${target_name} PRIVATE -O0 -g --coverage)
        target_link_options(${target_name} PRIVATE --coverage)
    endif()
endfunction()

set(SOLAR_HAVE_LIBCAMERA_FLAG 0)
set(SOLAR_HAVE_QT_FLAG 0)
set(SOLAR_HAVE_OPENCV_FLAG 0)

add_library(solar_tracker_core STATIC)
target_include_directories(solar_tracker_core PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
if(MSVC)
    target_compile_definitions(solar_tracker_core PRIVATE _CRT_SECURE_NO_WARNINGS)
endif()
solar_enable_coverage(solar_tracker_core)

add_subdirectory(common)
add_subdirectory(control)
add_subdirectory(hal)
add_subdirectory(actuators)
add_subdirectory(sensors)
add_subdirectory(system)
add_subdirectory(vision)
add_subdirectory(ui)
add_subdirectory(app)
add_subdirectory(qt)

target_compile_definitions(solar_tracker_core PUBLIC
    SOLAR_HAVE_LIBCAMERA=${SOLAR_HAVE_LIBCAMERA_FLAG}
    SOLAR_HAVE_QT=${SOLAR_HAVE_QT_FLAG}
    SOLAR_HAVE_OPENCV=${SOLAR_HAVE_OPENCV_FLAG}
)

enable_testing()
set(TESTING_SUPPORT_DIR ${CMAKE_SOURCE_DIR}/testing)
set(TEST_FRAMEWORK
    ${TESTING_SUPPORT_DIR}/test_main.cpp
    ${TESTING_SUPPORT_DIR}/test_common.hpp
)
add_subdirectory(common/test)
add_subdirectory(control/test)
add_subdirectory(actuators/test)
add_subdirectory(hal/test)
add_subdirectory(sensors/test)
add_subdirectory(system/test)
add_subdirectory(vision/test)

if(SOLAR_ENABLE_COVERAGE)
    find_program(GCOVR_EXECUTABLE gcovr)
    if(GCOVR_EXECUTABLE)
        add_custom_target(coverage
            COMMAND ${CMAKE_CTEST_COMMAND} --test-dir ${CMAKE_BINARY_DIR} --output-on-failure
            COMMAND ${GCOVR_EXECUTABLE}
                    -r ${CMAKE_SOURCE_DIR}
                    --filter ${CMAKE_SOURCE_DIR}/actuators
                    --filter ${CMAKE_SOURCE_DIR}/app
                    --filter ${CMAKE_SOURCE_DIR}/common
                    --filter ${CMAKE_SOURCE_DIR}/control
                    --filter ${CMAKE_SOURCE_DIR}/hal
                    --filter ${CMAKE_SOURCE_DIR}/qt
                    --filter ${CMAKE_SOURCE_DIR}/sensors
                    --filter ${CMAKE_SOURCE_DIR}/system
                    --filter ${CMAKE_SOURCE_DIR}/ui
                    --filter ${CMAKE_SOURCE_DIR}/vision
                    --exclude ${CMAKE_SOURCE_DIR}/testing
                    --xml-pretty -o ${CMAKE_BINARY_DIR}/coverage.xml
                    --html-details -o ${CMAKE_BINARY_DIR}/coverage.html
            WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
            COMMENT "Running tests and generating coverage report"
        )
    else()
        message(WARNING "SOLAR_ENABLE_COVERAGE=ON but gcovr was not found; no 'coverage' target will be created")
    endif()
endif()
