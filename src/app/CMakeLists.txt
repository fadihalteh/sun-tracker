target_sources(solar_tracker_core PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/AppConfig.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/CliController.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/LinuxEventLoop.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/SystemFactory.cpp
)

add_executable(solar_tracker ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp)
target_link_libraries(solar_tracker PRIVATE solar_tracker_core)
target_include_directories(solar_tracker PRIVATE ${CMAKE_SOURCE_DIR})
solar_enable_coverage(solar_tracker)
