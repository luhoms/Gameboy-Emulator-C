# Required so test files can access project headers
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../include)

# Find and configure Check (unit testing framework)
find_package(PkgConfig REQUIRED)
pkg_check_modules(CHECK REQUIRED check)

include_directories(${CHECK_INCLUDE_DIRS})
link_directories(${CHECK_LIBRARY_DIRS})

# Set test source files
set(TEST_SOURCES
  check_gbe.c
)

# Create the test executable
add_executable(check_gbe ${TEST_SOURCES})

# Link test executable with emu library and Check library
target_link_libraries(check_gbe emu ${CHECK_LIBRARIES})

# Add include path to test target
target_include_directories(check_gbe PRIVATE ${PROJECT_SOURCE_DIR}/include)

# Linux-specific: link additional POSIX libraries if using DEBIAN
find_program(DEBIAN "dpkg")
if(DEBIAN)
    target_link_libraries(check_gbe m rt pthread subunit)
endif()

# Windows-specific: if needed, point to Windows deps (optional)
if(WIN32)
    target_include_directories(emu PUBLIC ${PROJECT_SOURCE_DIR}/../windows_deps/check)
endif()