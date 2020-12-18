cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
#message(STATUS ${CMAKE_CURRENT_SOURCE_DIR})
set(PROJECT whitzard)
project(${PROJECT} C)

##############################################################################
# cross compiling
##############################################################################
if(CMAKE_CROSSCOMPILING)
  set(CMAKE_IGNORE_PATH
    /usr/lib/${CMAKE_HOST_SYSTEM_PROCESSOR}-linux-gnu/
    /usr/lib/${CMAKE_HOST_SYSTEM_PROCESSOR}-linux-gnu/lib/
  )
endif()






project(${PROJECT}_src)
message(STATUS ${CMAKE_HOST_SYSTEM_PROCESSOR})
message(STATUS ${CMAKE_CROSSCOMPILING})
set (COMPILE_FILES ${CMAKE_CURRENT_SOURCE_DIR}/main.c PARENT_SCOPE)
#install(TARGETS ${PROJECT} RUNTIME DESTINATION bin)