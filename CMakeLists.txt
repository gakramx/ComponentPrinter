cmake_minimum_required(VERSION 3.16)
project(Printer LANGUAGES CXX)

set(CMAKE_AUTOMOC ON)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Qt6 REQUIRED COMPONENTS Quick Qml)
find_package(Qt6 COMPONENTS PrintSupport)

# Define library target
add_library(Printer SHARED
    componentprinter_plugin.cpp
    printer.cpp
    componentprinter_plugin.h
    printer.h
)

# Set target properties
set_target_properties(Printer PROPERTIES
    AUTOMOC ON
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
)

# Handle PrintSupport dependency
if(Qt6PrintSupport_FOUND)
    target_link_libraries(Printer PRIVATE Qt6::PrintSupport)
else()
    target_compile_definitions(Printer PRIVATE QT_NO_PRINTER)
endif()

# Link required Qt libraries
target_link_libraries(Printer
    PRIVATE
        Qt6::Quick
        Qt6::Qml
)

# Installation paths
set(PLUGIN_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/lib/qt6/qml/com/foxmoxie/Printer")

# Install targets
install(TARGETS Printer
    RUNTIME DESTINATION ${PLUGIN_INSTALL_DIR}
    LIBRARY DESTINATION ${PLUGIN_INSTALL_DIR}
)

# Install QML module files
install(FILES qmldir
    DESTINATION ${PLUGIN_INSTALL_DIR}
)

# Copy qmldir file to build directory
add_custom_command(
    TARGET Printer POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy
        ${CMAKE_CURRENT_SOURCE_DIR}/qmldir
        ${CMAKE_BINARY_DIR}/lib/qmldir
)

# Set the URI for the QML module
set_target_properties(Printer PROPERTIES
    QT_QML_MODULE_URI "com.foxmoxie.Printer"
    QT_QML_MODULE_VERSION "1.0"
)
