cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

include(CheckCCompilerFlag)
include(cmake/exec.cmake)
include(cmake/library.cmake)
include(cmake/misc.cmake)
include(cmake/cpu.cmake)
#message(STATUS ${CMAKE_CURRENT_SOURCE_DIR})
set(PROJECT whitzard)
project(${PROJECT} C)


##############################################################################
# WHITZARD Version
##############################################################################
execute_process(
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
  COMMAND scripts/version
  OUTPUT_VARIABLE WHITZARD_VERSION
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
string(REPLACE "-" ";" WHITZARD_LIB_VERSION ${WHITZARD_VERSION})
list(GET WHITZARD_LIB_VERSION 0 WHITZARD_LIB_VERSION)

##############################################################################
# cross compiling
##############################################################################
if(CMAKE_CROSSCOMPILING)
  set(CMAKE_IGNORE_PATH
    /usr/lib/${CMAKE_HOST_SYSTEM_PROCESSOR}-linux-gnu/
    /usr/lib/${CMAKE_HOST_SYSTEM_PROCESSOR}-linux-gnu/lib/
  )
endif()
set(CMAKE_C_COMPILER_TARGET ${CMAKE_SYSTEM_PROCESSOR}-linux-gnu)


##############################################################################
# build config
##############################################################################
check_c_compiler_flag("-Wno-address-of-packed-member"
		      compiler_flag_no_address_of_packed_member)
set(WHITZARD_RUNTIME_DIR "bin" CACHE STRING "Relative runtime directory path")
set(WHITZARD_LIBRARY_DIR "lib" CACHE STRING "Relative library directory path")

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/${WHITZARD_RUNTIME_DIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/${WHITZARD_LIBRARY_DIR})

if (CMAKE_BUILD_TYPE)
  set(CMAKE_C_FLAGS "-g -fPIC -Werror -Wall ${CMAKE_C_FLAGS}")
endif()

if (compiler_flag_no_address_of_packed_member)
  set(CMAKE_C_FLAGS "-Wno-address-of-packed-member ${CMAKE_C_FLAGS}")
endif()



# release
list(APPEND BUILD_TYPES "release")
string(CONCAT CMAKE_C_FLAGS_RELEASE
  "-O2 "
  "-fstack-protector "
  "-DFORTIFY_SOURCE=2 "
  "-fno-common "
)

string(CONCAT CMAKE_EXE_LINKER_FLAGS_RELEASE "-pie")

# debug
list(APPEND BUILD_TYPES "debug")
string(CONCAT CMAKE_C_FLAGS_DEBUG
  "-O0 "
  "-DCLIB_DEBUG "
  "-fstack-protector "
  "-DFORTIFY_SOURCE=2 "
  "-fno-common "
)
string(REPLACE ";" " " BUILD_TYPES "${BUILD_TYPES}")
set_property(CACHE CMAKE_BUILD_TYPE PROPERTY
	     HELPSTRING "Build type - valid options are: ${BUILD_TYPES}")
##############################################################################
# 添加搜索的头文件位置
##############################################################################
include_directories (
    ${CMAKE_SOURCE_DIR}/src
    ${CMAKE_BINARY_DIR}
    ${CMAKE_BINARY_DIR}/include
)
##############################################################################
# subdirs - order matters
##############################################################################
set(SUBDIRS
    main
    hello
    vppinfra
)

foreach(DIR ${SUBDIRS})
  add_subdirectory(${DIR})
endforeach()

#message(STATUS ${CMAKE_HOST_SYSTEM_PROCESSOR})
#message(STATUS ${CMAKE_CROSSCOMPILING})
#message(STATUS ${CMAKE_BINARY_DIR})
#message(STATUS ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
#message(STATUS ${CMAKE_LIBRARY_OUTPUT_DIRECTORY})
#message(STATUS ${CMAKE_BUILD_TYPE})
#message(STATUS ${BUILD_TYPES})
#message(STATUS ${HELPSTRING})
#message(STATUS ${CMAKE_SOURCE_DIR})


#install(TARGETS ${PROJECT} RUNTIME DESTINATION bin)



##############################################################################
# print configuration
##############################################################################
pr("WHITZARD version" "${WHITZARD_VERSION}")
pr("WHITZARD library version" "${WHITZARD_LIB_VERSION}")
pr("C flags" "${CMAKE_C_FLAGS}${CMAKE_C_FLAGS_${CMAKE_BUILD_TYPE_UC}}")
pr("Linker flags (apps)" "${CMAKE_EXE_LINKER_FLAGS}${CMAKE_EXE_LINKER_FLAGS_${CMAKE_BUILD_TYPE_UC}}")
pr("Linker flags (libs)" "${CMAKE_SHARED_LINKER_FLAGS}${CMAKE_SHARED_LINKER_FLAGS_${CMAKE_BUILD_TYPE_UC}}")
pr("Install prefix" "${CMAKE_INSTALL_PREFIX}")