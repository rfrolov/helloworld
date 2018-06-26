cmake_minimum_required(VERSION 3.2)

project(helloworld VERSION 0.0.$ENV{TRAVIS_BUILD_NUMBER})

add_subdirectory(googletest)
include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})
enable_testing()

configure_file(version.h.in ${PROJECT_SOURCE_DIR}/version.h)

add_executable(${PROJECT_NAME} main.cpp)

add_library(${PROJECT_NAME}_lib lib.cpp)

add_executable(test_${PROJECT_NAME} test_main.cpp)

set_target_properties(
        ${PROJECT_NAME} test_${PROJECT_NAME} PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
        COMPILE_OPTIONS -Wpedantic -Wall -Wextra
)

target_link_libraries(${PROJECT_NAME} ${PROJECT_NAME}_lib)

target_link_libraries(
        test_${PROJECT_NAME}
        ${Boost_LIBRARIES}
        ${PROJECT_NAME}_lib
)

install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT rfrolov@engineer.com)

include(CPack)

target_link_libraries(
        test_${PROJECT_NAME}
        gtest
        pthread
)

add_test(tests test_${PROJECT_NAME})
