cmake_minimum_required (VERSION 3.16)

project (ipp_cpp)

set(IPP_PATH "$ENV{ONEAPI_ROOT}ipp/latest/lib/cmake/ipp")
string(REPLACE "\\" "/" IPP_PATH ${IPP_PATH})
set(IPP_INCLUDE_DIRS "$ENV{ONEAPI_ROOT}ipp/latest/include")
string(REPLACE "\\" "/" IPP_INCLUDE_DIRS ${IPP_INCLUDE_DIRS})
set(IPP_SHARED FALSE)

find_package(IPP REQUIRED CONFIG PATHS ${IPP_PATH})
find_package(GTest CONFIG)

if(IPP_FOUND)
	add_compile_definitions(HAS_IPP)
else()
	message(FATAL_ERROR "Intel IPP library was not found")
endif()

add_library (${PROJECT_NAME} INTERFACE)

target_include_directories(${PROJECT_NAME} INTERFACE 
	${IPP_INCLUDE_DIRS}
	include
)

target_link_libraries(${PROJECT_NAME} INTERFACE IPP::ippcc IPP::ippch IPP::ippcore IPP::ippdc IPP::ippe IPP::ippvm
)

if(GTest_FOUND)
	enable_testing()
	add_subdirectory("test")
endif()