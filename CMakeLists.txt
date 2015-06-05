project(etw-inspector CXX)
cmake_minimum_required(VERSION 2.6.2)

set(CMAKE_SUPPRESS_REGENERATION TRUE)

# Force pedantic and Wall flags.
if(MSVC)
  # Activate warning level 4.
  # Disable 4481 warning C4481: nonstandard extension used: override
  # specifier 'override'
  if(CMAKE_CXX_FLAGS MATCHES "/W[0-4]")
    string(REGEX REPLACE "/W[0-4]" "/W4 /wd4481"
           CMAKE_CXX_FLAGS
           "${CMAKE_CXX_FLAGS}")
  else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
  endif()
elseif(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_GNUCXX)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wno-long-long -pedantic-errors")
endif()

# Force a unicode project.
if(MSVC)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /D _UNICODE /D UNICODE")
endif()

# Add thread library.
if(MSVC)
  set(PTHREAD_LIB "")
else()
  set(PTHREAD_LIB "pthread")
endif()

# Find the location of gmock library.
find_path(GMOCK_FOLDER NAMES gmock/CMakeLists.txt
          HINTS .. third_party ../third_party)
if(GMOCK_FOLDER)
  set(GMOCK_ROOT ${GMOCK_FOLDER}/gmock)
  set(GMOCK_FOUND 1)
else()
  message("gmock folder not found.")
endif(GMOCK_FOLDER)

# Add include search paths for gmock.
if(GMOCK_FOUND)
  include_directories(
      ${GMOCK_ROOT}/include
      ${GMOCK_ROOT}
      ${GMOCK_ROOT}/gtest/include
      ${GMOCK_ROOT}/gtest
      )
endif(GMOCK_FOUND)

# Add the etw-inspector default include path.
include_directories(src)

####################
# Libraries
####################

add_library(base
    src/base/base.h
    src/base/logging.cc
    src/base/logging.h
    src/base/string_utils.cc
    src/base/string_utils.h
    ${BASE_WIN_SOURCES}
    )

add_library(event
    src/event/event.cc
    src/event/event.h
    src/event/utils.cc
    src/event/utils.h
    src/event/value.cc
    src/event/value.h
    )
target_link_libraries(event
    base
    )

if(MSVC)
   set(WINDOWS_UNITTEST "")
else()
  set(WINDOWS_UNITTEST "")
endif()

####################
# Unit tests
####################

if(GMOCK_FOUND)
add_executable(unittests
    src/base/logging_unittest.cc
    src/base/string_utils_unittest.cc
    src/event/event_unittest.cc
    src/event/utils_unittest.cc
    src/event/value_unittest.cc
    ${WINDOWS_UNITTEST}
    ${GMOCK_ROOT}/gtest/src/gtest-all.cc
    ${GMOCK_ROOT}/src/gmock-all.cc
    ${GMOCK_ROOT}/src/gmock_main.cc
    )

target_link_libraries(unittests
    base
    event
    ${PTHREAD_LIB}
    )
endif(GMOCK_FOUND)
