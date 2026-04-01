add_executable(test_vision
    ${TEST_FRAMEWORK}
    ${CMAKE_CURRENT_SOURCE_DIR}/test_suntracker.cpp
)
target_include_directories(test_vision PRIVATE ${CMAKE_SOURCE_DIR} ${TESTING_SUPPORT_DIR})
target_link_libraries(test_vision PRIVATE solar_tracker_core)
solar_enable_coverage(test_vision)

add_test(NAME SunTracker_NoBrightPixels_ConfidenceZero
         COMMAND test_vision --run SunTracker_NoBrightPixels_ConfidenceZero)
add_test(NAME SunTracker_BrightSpot_Gray8Packed_CentroidApproxCorrect
         COMMAND test_vision --run SunTracker_BrightSpot_Gray8Packed_CentroidApproxCorrect)
add_test(NAME SunTracker_BrightSpot_Gray8PaddedStride_CentroidApproxCorrect
         COMMAND test_vision --run SunTracker_BrightSpot_Gray8PaddedStride_CentroidApproxCorrect)
add_test(NAME SunTracker_BrightSpot_RGB888_CentroidApproxCorrect
         COMMAND test_vision --run SunTracker_BrightSpot_RGB888_CentroidApproxCorrect)
add_test(NAME SunTracker_BrightSpot_BGR888_CentroidApproxCorrect
         COMMAND test_vision --run SunTracker_BrightSpot_BGR888_CentroidApproxCorrect)
add_test(NAME SunTracker_InvalidBuffer_DoesNotEmitEstimate
         COMMAND test_vision --run SunTracker_InvalidBuffer_DoesNotEmitEstimate)
