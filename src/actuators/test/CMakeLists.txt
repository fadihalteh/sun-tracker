add_executable(test_actuators
    ${TEST_FRAMEWORK}
    ${CMAKE_CURRENT_SOURCE_DIR}/test_actuatormanager.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/test_pca9685.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/test_servodriver.cpp
    ${TESTING_SUPPORT_DIR}/FakeI2CDevice.hpp
)
target_include_directories(test_actuators PRIVATE ${CMAKE_SOURCE_DIR} ${TESTING_SUPPORT_DIR})
target_link_libraries(test_actuators PRIVATE solar_tracker_core)
solar_enable_coverage(test_actuators)

add_executable(servo_manual_smoketest
    ${CMAKE_SOURCE_DIR}/actuators/test/servo_manual_smoketest.cpp
)
target_include_directories(servo_manual_smoketest PRIVATE ${CMAKE_SOURCE_DIR})
target_link_libraries(servo_manual_smoketest PRIVATE solar_tracker_core)
solar_enable_coverage(servo_manual_smoketest)

add_test(NAME ActuatorManager_first_command_is_saturated_without_history_limit
         COMMAND test_actuators --run ActuatorManager_first_command_is_saturated_without_history_limit)
add_test(NAME ActuatorManager_subsequent_commands_are_rate_limited_per_channel
         COMMAND test_actuators --run ActuatorManager_subsequent_commands_are_rate_limited_per_channel)
add_test(NAME ActuatorManager_saturation_happens_before_rate_limit
         COMMAND test_actuators --run ActuatorManager_saturation_happens_before_rate_limit)
add_test(NAME ActuatorManager_runtime_like_large_max_step_effectively_disables_slew_limit
         COMMAND test_actuators --run ActuatorManager_runtime_like_large_max_step_effectively_disables_slew_limit)
add_test(NAME ActuatorManager_callback_can_reenter_onCommand_without_deadlock
         COMMAND test_actuators --run ActuatorManager_callback_can_reenter_onCommand_without_deadlock)

add_test(NAME PCA9685_init_writes_mode_and_prescale
         COMMAND test_actuators --run PCA9685_init_writes_mode_and_prescale)
add_test(NAME PCA9685_set_pwm_writes_led_register_block
         COMMAND test_actuators --run PCA9685_set_pwm_writes_led_register_block)
add_test(NAME PCA9685_set_pulse_us_uses_frequency_conversion
         COMMAND test_actuators --run PCA9685_set_pulse_us_uses_frequency_conversion)

add_test(NAME ServoDriver_log_only_mode_starts_without_hardware
         COMMAND test_actuators --run ServoDriver_log_only_mode_starts_without_hardware)
add_test(NAME ServoDriver_require_hardware_fails_fast_when_unavailable
         COMMAND test_actuators --run ServoDriver_require_hardware_fails_fast_when_unavailable)
add_test(NAME ServoDriver_prefer_hardware_falls_back_to_log_only_when_unavailable
         COMMAND test_actuators --run ServoDriver_prefer_hardware_falls_back_to_log_only_when_unavailable)
add_test(NAME ServoDriver_require_hardware_with_injected_pca_enters_hardware_mode
         COMMAND test_actuators --run ServoDriver_require_hardware_with_injected_pca_enters_hardware_mode)
add_test(NAME ServoDriver_apply_while_stopped_writes_nothing
         COMMAND test_actuators --run ServoDriver_apply_while_stopped_writes_nothing)
add_test(NAME ServoDriver_start_without_parking_does_not_write_servo_channels
         COMMAND test_actuators --run ServoDriver_start_without_parking_does_not_write_servo_channels)
add_test(NAME ServoDriver_start_parks_to_neutral_when_enabled
         COMMAND test_actuators --run ServoDriver_start_parks_to_neutral_when_enabled)
add_test(NAME ServoDriver_stop_parks_to_neutral_when_enabled
         COMMAND test_actuators --run ServoDriver_stop_parks_to_neutral_when_enabled)
add_test(NAME ServoDriver_apply_clamps_and_writes_channels
         COMMAND test_actuators --run ServoDriver_apply_clamps_and_writes_channels)
add_test(NAME ServoDriver_neutral_degree_maps_midway_between_low_and_high
         COMMAND test_actuators --run ServoDriver_neutral_degree_maps_midway_between_low_and_high)
