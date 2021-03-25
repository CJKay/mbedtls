cmake_minimum_required(VERSION 2.8.12)

#
# Simulate configuring and building Mbed TLS as the user might do it. We'll
# install into a directory inside our own build directory.
#

set(MbedTLS_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../..")
set(MbedTLS_INSTALL_DIR "${CMAKE_CURRENT_BINARY_DIR}/mbedtls")
set(MbedTLS_BINARY_DIR "${MbedTLS_INSTALL_DIR}${CMAKE_FILES_DIRECTORY}")

execute_process(
    COMMAND "${CMAKE_COMMAND}"
        -S "${MbedTLS_SOURCE_DIR}"
        -B "${MbedTLS_BINARY_DIR}")

execute_process(
    COMMAND "${CMAKE_COMMAND}"
        --build "${MbedTLS_BINARY_DIR}")

execute_process(
    COMMAND_ERROR_IS_FATAL ANY
    COMMAND "${CMAKE_COMMAND}"
        --install "${MbedTLS_BINARY_DIR}")

#
# Locate the package.
#

set(MbedTLS_DIR "${MbedTLS_INSTALL_DIR}/cmake")
find_package(MbedTLS COMPONENTS Crypto TLS X509 REQUIRED)

#
# At this point, the Mbed TLS targets should have been imported, and we can now
# link to them from our own program.
#

add_executable(cmake_package_install cmake_package_install.c)
target_link_libraries(cmake_package_install
    MbedTLS::Crypto MbedTLS::TLS MbedTLS::X509)
