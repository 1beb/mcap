cmake_minimum_required(VERSION 3.10)
project(McapTest CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if (MSVC)
  add_compile_options(/W4 /WX
    /wd4251 # suppress warning about having template instances (such as std::string) as public class members
    /wd4267 # '+=': conversion from 'size_t' to 'uint32_t', possible loss of data
    /wd4996 # 'fopen': This function or variable may be unsafe. Consider using fopen_s instead.
            # To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.
    /wd4244 # 'argument': conversion from 'uint64_t' to 'long', possible loss of data
  )
  add_compile_definitions(MCAP_BUILDING_DLL)
else()
  add_compile_options(-Wall -Wextra -pedantic -Werror)
endif()

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

add_executable(streamed-reader-conformance streamed_reader_conformance.cpp)
target_link_libraries(streamed-reader-conformance ${CONAN_LIBS})

add_executable(streamed-writer-conformance streamed_writer_conformance.cpp)
target_link_libraries(streamed-writer-conformance ${CONAN_LIBS})

add_executable(unit-tests unit_tests.cpp)
target_link_libraries(unit-tests ${CONAN_LIBS})
