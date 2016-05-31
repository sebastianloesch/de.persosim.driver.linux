cmake_minimum_required(VERSION 3.0)
project("de.persosim.driver.unix" VERSION 0.8.0)

if (${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
	set(IS_LINUX TRUE)
endif()

if(!APPLE AND !IS_LINUX)
	message(FATAL_ERROR "Currently only OS X and Linux platforms are supported")
endif()


if(IS_LINUX)
	file(GLOB SRC_FILES *.c)
elseif(APPLE)
	file(GLOB SRC_FILES *.c MacOSX/*.c)
endif()
list(REMOVE_ITEM SRC_FILES ${PROJECT_SOURCE_DIR}/hexStringTest.c)


# Build the library
set(LIB_NAME ifdhPersoSim)
set(CMAKE_C_FLAGS "-g -std=gnu99 -fpic")
add_definitions(-DPCSC_DEBUG=1)
#add_definitions(-DATR_DEBUG=1)
if(IS_LINUX)
	include_directories(${PROJECT_SOURCE_DIR} /usr/include/PCSC)
elseif(APPLE)
	include_directories(${PROJECT_SOURCE_DIR} ${PROJECT_SOURCE_DIR}/MacOSX)
	add_definitions(-DUSE_SYSLOG -DRESPONSECODE_DEFINED_IN_WINTYPES_H)
endif()
add_library(${LIB_NAME} SHARED ${SRC_FILES})


# Create the installation files
if(IS_LINUX)
	set(LIBPATH "/usr/lib/pcsc/drivers/serial/${CMAKE_SHARED_LIBRARY_PREFIX}${LIB_NAME}${CMAKE_SHARED_LIBRARY_SUFFIX}")
elseif(APPLE)
	set(BUNDLEPATH "/usr/local/libexec/SmartCardServices/drivers/${LIB_NAME}.bundle")
	set(LIBPATH ${BUNDLEPATH})
	set(CFBUNDLEEXECUTABLE ${CMAKE_SHARED_LIBRARY_PREFIX}${LIB_NAME}${CMAKE_SHARED_LIBRARY_SUFFIX})
	configure_file("${PROJECT_SOURCE_DIR}/MacOSX/Info.plist.in" "${PROJECT_BINARY_DIR}/Info.plist")
endif()
configure_file("${PROJECT_SOURCE_DIR}/reader.conf.in" "${PROJECT_BINARY_DIR}/reader.conf")


# Specify installations files and locations
if(IS_LINUX)
	install(TARGETS ${LIB_NAME} DESTINATION "/usr/lib/pcsc/drivers/serial/")
	install(FILES ${PROJECT_BINARY_DIR}/reader.conf DESTINATION "/etc/reader.conf.d/" RENAME "${LIB_NAME}.conf")
elseif(APPLE)
	install(FILES ${PROJECT_BINARY_DIR}/Info.plist DESTINATION "${BUNDLEPATH}/Contents")
	install(TARGETS ${LIB_NAME} DESTINATION "${BUNDLEPATH}/Contents/MacOS")
	install(FILES ${PROJECT_BINARY_DIR}/reader.conf DESTINATION "/etc/")
endif()


# Build the test (you may run the test by invoking make test)
enable_testing()
include(CTest)
add_executable(hexStringTest hexStringTest.c hexString.c)
add_test(NAME hexStringTest COMMAND ${CMAKE_BINARY_DIR}/hexStringTest)
