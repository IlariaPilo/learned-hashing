cmake_minimum_required(VERSION 3.19)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -march=native -pthread")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -O0 -g -fsanitize=address,leak,undefined")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3")

# ==== Non compatible compile flags ====
include(CheckCXXCompilerFlag)
check_cxx_compiler_flag(-static-libsan HAS_LIBSAN)
if (HAS_LIBSAN)
  set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -static-libsan")
else()
  set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -static-libasan")
endif()

# ==== Test target ====
set(TEST_TARGET lh_tests)
add_executable(${TEST_TARGET} tests.cpp)

include(${PROJECT_SOURCE_DIR}/thirdparty/googletest.cmake)
target_link_libraries(${TEST_TARGET} PRIVATE ${PROJECT_NAME} ${COMPETITORS_LIBRARY} ${GOOGLETEST_LIBRARY})

# ==== Benchmark executable ====
add_executable(lh_benchmarks benchmarks.cpp)
target_link_libraries(lh_benchmarks PRIVATE learned-hashing ${GOOGLEBENCHMARK_LIBRARY})

# ==== Function Stats executable ====
add_executable(lh_stats stats.cpp)
target_link_libraries(lh_stats PRIVATE learned-hashing ${GOOGLETEST_LIBRARY})
