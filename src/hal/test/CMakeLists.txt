if(UNIX AND NOT APPLE)
    add_executable(test_linux_i2c_hw
        ${CMAKE_CURRENT_SOURCE_DIR}/test_linux_i2c_hw.cpp
    )
    target_include_directories(test_linux_i2c_hw PRIVATE ${CMAKE_SOURCE_DIR} ${TESTING_SUPPORT_DIR})
    target_link_libraries(test_linux_i2c_hw PRIVATE solar_tracker_core)
    solar_enable_coverage(test_linux_i2c_hw)

    add_test(NAME LinuxI2C_PCA9685_hw_init_and_write COMMAND test_linux_i2c_hw)
    set_tests_properties(LinuxI2C_PCA9685_hw_init_and_write PROPERTIES LABELS "hw;i2c;integration" SKIP_RETURN_CODE 77)
endif()
