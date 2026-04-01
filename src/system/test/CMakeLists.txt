add_executable(test_system
    ${TEST_FRAMEWORK}
    ${CMAKE_CURRENT_SOURCE_DIR}/test_systemmanager_statemachine.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/test_trajectory_circular.cpp
)
target_include_directories(test_system PRIVATE ${CMAKE_SOURCE_DIR} ${TESTING_SUPPORT_DIR})
target_link_libraries(test_system PRIVATE solar_tracker_core)
solar_enable_coverage(test_system)

add_test(NAME Trajectory_CircularSetpoints_ProduceValidServoOutputs
         COMMAND test_system --run Trajectory_CircularSetpoints_ProduceValidServoOutputs)
add_test(NAME SystemManager_start_to_searching_then_tracking_on_bright_frame
         COMMAND test_system --run SystemManager_start_to_searching_then_tracking_on_bright_frame)
add_test(NAME SystemManager_manual_mode_emits_commands
         COMMAND test_system --run SystemManager_manual_mode_emits_commands)
add_test(NAME SystemManager_start_with_null_camera_enters_fault_and_fails
         COMMAND test_system --run SystemManager_start_with_null_camera_enters_fault_and_fails)
add_test(NAME SystemManager_start_when_servo_driver_requires_missing_hardware_enters_fault
         COMMAND test_system --run SystemManager_start_when_servo_driver_requires_missing_hardware_enters_fault)
add_test(NAME SystemManager_start_when_camera_start_fails_enters_fault
         COMMAND test_system --run SystemManager_start_when_camera_start_fails_enters_fault)
