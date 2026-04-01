find_package(Qt5 QUIET COMPONENTS Widgets Charts)

if(Qt5Widgets_FOUND)
    message(STATUS "Qt5 found -> building solar_tracker_qt")
    set(SOLAR_HAVE_QT_FLAG 1 PARENT_SCOPE)
    set(SOLAR_HAVE_QT_FLAG 1)

    add_executable(solar_tracker_qt
        ${CMAKE_CURRENT_SOURCE_DIR}/main_qt.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/MainWindow.cpp
    )

    set_target_properties(solar_tracker_qt PROPERTIES
        AUTOMOC ON
        AUTOUIC ON
        AUTORCC ON
    )

    target_include_directories(solar_tracker_qt PRIVATE ${CMAKE_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(solar_tracker_qt PRIVATE solar_tracker_core Qt5::Widgets)

    if(TARGET Qt5::Charts)
        target_link_libraries(solar_tracker_qt PRIVATE Qt5::Charts)
        target_compile_definitions(solar_tracker_qt PRIVATE SOLAR_HAVE_QT_CHARTS=1)
    else()
        target_compile_definitions(solar_tracker_qt PRIVATE SOLAR_HAVE_QT_CHARTS=0)
    endif()

    solar_enable_coverage(solar_tracker_qt)
else()
    message(STATUS "Qt5 not found -> Qt GUI disabled (no solar_tracker_qt target)")
endif()
