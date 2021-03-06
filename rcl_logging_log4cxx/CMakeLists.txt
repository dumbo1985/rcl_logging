cmake_minimum_required(VERSION 3.5)

project(rcl_logging_log4cxx)

# Default to C11
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 11)
endif()
# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

find_package(ament_cmake_ros REQUIRED)
list(INSERT CMAKE_MODULE_PATH 0 "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
find_package(Log4cxx REQUIRED)

include_directories(${ament_INCLUDE_DIRS} include)


if(NOT WIN32)
  add_compile_options(-Wall -Wextra -Wpedantic)
else()
  # Disable ddl-interface warnings for STL from log4cxx code, because we are already 
  # forced to use the same compiler in Windows, as we are getting the binaries
  # for log4cxx from Chocolatey
  # https://web.archive.org/web/20130317015847/http://connect.microsoft.com/VisualStudio/feedback/details/696593/vc-10-vs-2010-basic-string-exports
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4275 /wd4251")
endif()

set(${PROJECT_NAME}_sources
    src/rcl_logging_log4cxx/rcl_logging_log4cxx.cpp
)
#set_source_files_properties(${${PROJECT_NAME}_sources} PROPERTIES language "CXX")

if(NOT WIN32)
  add_library(${PROJECT_NAME} ${${PROJECT_NAME}_sources})
else()
  add_library(${PROJECT_NAME} STATIC ${${PROJECT_NAME}_sources})
endif()

ament_target_dependencies(${PROJECT_NAME}
    Log4cxx
)

target_compile_definitions(${PROJECT_NAME} PRIVATE "RCL_LOGGING_BUILDING_DLL")

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

ament_export_include_directories(include)
ament_export_dependencies(ament_cmake)
ament_export_libraries(${PROJECT_NAME} log4cxx)
ament_package()
