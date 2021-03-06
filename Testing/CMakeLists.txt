cmake_minimum_required(VERSION 3.12)
project(testing)

set(CMAKE_CXX_STANDARD 17)
#set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(DEPRECATION_FLAG "-Wno-deprecated-declarations")
# Download and unpack googletest at configure time
configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
        RESULT_VARIABLE result
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
    message(FATAL_ERROR "CMake step for googletest failed: ${result}")
endif()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
        RESULT_VARIABLE result
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
    message(FATAL_ERROR "Build step for googletest failed: ${result}")
endif()

# Prevent overriding the parent project's compiler/linker
# settings on Windows
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Add googletest directly to our build. This defines
# the gtest and gtest_main targets.
add_subdirectory(${CMAKE_CURRENT_BINARY_DIR}/googletest-src
        ${CMAKE_CURRENT_BINARY_DIR}/googletest-build
        EXCLUDE_FROM_ALL)

# The gtest/gtest_main targets carry header search path
# dependencies automatically when using CMake 2.8.11 or
# later. Otherwise we have to add them here ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
    include_directories("${gtest_SOURCE_DIR}/include")
endif()

# Now simply link against gtest or gtest_main as needed. Eg

include_directories(cmake-build-debug/googletest-src/googletest/include)
include_directories(cmake-build-debug/googletest-src/googlemock/include)

add_compile_definitions("_HIPPOMOCKS__ENABLE_CFUNC_MOCKING_SUPPORT")
include_directories(../hippomocks/HippoMocks)
include_directories(src)

set(SOURCE_FILES
        main.cpp
        src/TestClass.cpp
        DependencyBreaking/TestHippomock.cpp
        DependencyBreaking/TestGMock.cpp
        DependencyBreaking/TestPackageWriter.cpp
        Cpp14/TestSmartPointer.cpp
        Language/TestMath.cpp
        Language/TestStruct.cpp
        TestStl/TestIo.cpp
        DependencyBreaking/TestFactory.cpp
#        src/Common/Path.cpp
        src/Common/FileSystemUtility.cpp
        src/PackageWriter.cpp
        )

add_executable(testing ${SOURCE_FILES})

target_link_libraries(testing gtest gmock gtest_main)
