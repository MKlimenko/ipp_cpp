cmake_minimum_required (VERSION 3.16)

project(ipp_cpp_test)

add_executable(${PROJECT_NAME} test.cpp)

set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 20)
target_link_libraries(${PROJECT_NAME} 
										ipp_cpp
										GTest::gtest 
										GTest::gtest_main 
)

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
	target_compile_options(${PROJECT_NAME} PUBLIC -ftime-trace)
endif()

enable_testing()
add_test(NAME ${PROJECT_NAME} COMMAND ${PROJECT_NAME})