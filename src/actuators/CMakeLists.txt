target_sources(solar_tracker_core PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/ActuatorManager.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/PCA9685.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/ServoDriver.cpp
)
