cmake_minimum_required(VERSION 3.15 FATAL_ERROR)
project(ExampleProject LANGUAGES CXX)
set(CMAKE_CXX_STANDARD 17)

macro(DATE RESULT)
	if(WIN32)
		execute_process(COMMAND "cmd" " /C date /T" OUTPUT_VARIABLE ${RESULT})
		string(REGEX REPLACE "(..)/(..)/(....).*" "\\3-\\2-\\1" ${RESULT} ${${RESULT}})
	elseif(UNIX)
		execute_process(COMMAND "date" "+%Y-%m-%d" OUTPUT_VARIABLE ${RESULT})
	else()
		message(SEND_ERROR "Unable to detect date")
		set(${RESULT} UNKNOWN)
	endif()
    string(REGEX REPLACE "\r\n$" "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\n$" "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\"" "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\ " "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\-" "" ${RESULT} "${${RESULT}}")
endmacro()

macro(TIME RESULT)
	if(WIN32)
		execute_process(COMMAND "cmd" " /C echo %TIME%" OUTPUT_VARIABLE ${RESULT})
		string(REGEX REPLACE "(..:..:..),(..)" "\\1" ${RESULT} ${${RESULT}})
	elseif(UNIX)
		execute_process(COMMAND "date" "+%H:%M:%S" OUTPUT_VARIABLE ${RESULT})
	else()
		message(SEND_ERROR "Unable to detect time")
		set(${RESULT} UNKNOWN)
	endif()
    string(REGEX REPLACE "\r\n$" "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\n$" "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\"" "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\:" "" ${RESULT} "${${RESULT}}")
    string(REGEX REPLACE "\ " "" ${RESULT} "${${RESULT}}")
endmacro()

DATE(TODAY)
TIME(NOW)

string(SUBSTRING ${TODAY} 2 -1 TODAY)
string(SUBSTRING ${NOW} 0 4 NOW)
set(DATETIME ${TODAY}${NOW})

set(PRODUCT_VERSION_MAJOR 0)
set(PRODUCT_VERSION_MINOR 0)
set(PRODUCT_VERSION_PATCH 0)
set(PRODUCT_VERSION_BUILD_NUMBER ${DATETIME})

set(COMPANY_NAME "CLE Inc.")
set(FILE_DESCRIPTION "CLE's Example Project")
set(INTERNAL_NAME "CLE's Example Project")
set(ORIGINAL_FILENAME "ExampleProject.dll")
set(LEGAL_COPYRIGHT "CLE Inc. All rights reserved.")
set(PRODUCT_NAME "CLE's Example Project")

configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/version.rc.in
  ${CMAKE_CURRENT_BINARY_DIR}/version.rc
  @ONLY)

list(APPEND INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/include)

list(APPEND SRCS src/main.cpp)

add_executable(ExampleProject ${SRCS} ${CMAKE_CURRENT_BINARY_DIR}/version.rc)
target_include_directories(ExampleProject PRIVATE ${INCLUDE_DIRS})
# target_link_directories(ExampleProject PRIVATE ${LINK_DIRS})