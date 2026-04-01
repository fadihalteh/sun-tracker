add_executable(test_control
    ${TEST_FRAMEWORK}
    ${CMAKE_CURRENT_SOURCE_DIR}/test_controller.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/test_kinematics.cpp
)
target_include_directories(test_control PRIVATE ${CMAKE_SOURCE_DIR} ${TESTING_SUPPORT_DIR})
target_link_libraries(test_control PRIVATE solar_tracker_core)
solar_enable_coverage(test_control)

add_test(NAME Controller_LowConfidence_NoMotion
         COMMAND test_control --run Controller_LowConfidence_NoMotion)
add_test(NAME Controller_WithinDeadband_NoMotion
         COMMAND test_control --run Controller_WithinDeadband_NoMotion)
add_test(NAME Controller_OutsideDeadband_ProducesMotion
         COMMAND test_control --run Controller_OutsideDeadband_ProducesMotion)
add_test(NAME Controller_OutputClamped
         COMMAND test_control --run Controller_OutputClamped)

add_test(NAME Kinematics3RRS_outputs_in_range_and_integer_like
         COMMAND test_control --run Kinematics3RRS_outputs_in_range_and_integer_like)
add_test(NAME Kinematics3RRS_continuity_small_setpoint_changes_small_output_changes
         COMMAND test_control --run Kinematics3RRS_continuity_small_setpoint_changes_small_output_changes)
add_test(NAME Kinematics3RRS_invalid_geometry_is_surfaced_explicitly
         COMMAND test_control --run Kinematics3RRS_invalid_geometry_is_surfaced_explicitly)
