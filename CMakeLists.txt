cmake_minimum_required(VERSION 2.4)

project(OISB)

set(VERSION 0.1.0)

set(OISB_OIS_INCLUDE_DIR "" CACHE PATH "")
set(OISB_OIS_LIB "" CACHE FILEPATH "")

FILE(GLOB sources   src/*.cpp)
FILE(GLOB includes  include/*.h)

# general flags
add_definitions(-D_LIB -D_CRT_SECURE_NO_WARNINGS -DOISB_STATIC_LIB)

include_directories(include)
include_directories(${OISB_OIS_INCLUDE_DIR})

# cpack
set(CPACK_PACKAGE_DESCRIPTION "OISB - Object Oriented Input System Binding library")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Wraps OIS and provides means to make action binding/mapping.")
set(CPACK_PACKAGE_NAME "oisb")
set(CPACK_DEBIAN_PACKAGE_DEPENDS "ois")
set(CPACK_PACKAGE_CONTACT "preisler.m@gmail.com")
set(CPACK_PACKAGE_VENDOR "preisler.m@gmail.com")
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE")

SET(CPACK_GENERATOR ZIP)
IF(UNIX)
  SET(CPACK_GENERATOR ${CPACK_GENERATOR};STGZ;TGZ)
ENDIF(UNIX)
IF(LINUX)
SET(CPACK_GENERATOR ${CPACK_GENERATOR};DEB;RPM)
ENDIF(LINUX)
IF(MSVC)
  SET(CPACK_GENERATOR ${CPACK_GENERATOR};NSIS)
ENDIF(MSVC)
IF(APPLE)
  SET(CPACK_GENERATOR ${CPACK_GENERATOR};PackageMaker)
ENDIF(APPLE)
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${VERSION}-${CMAKE_SYSTEM_PROCESSOR}")
include(CPack)

install(DIRECTORY include/
        DESTINATION include
        FILES_MATCHING PATTERN "*.h"
                       PATTERN ".svn" EXCLUDE
       )
# oisb library
add_library(oisb STATIC ${sources} ${includes})

install(TARGETS oisb ARCHIVE DESTINATION lib)

# oisb simple demo
include_directories(demo/simple)
add_executable(oisb_console demos/simple/OISBConsole.cpp demos/simple/CommandLine.rc)
target_link_libraries(oisb_console oisb ${OISB_OIS_LIB})

install(TARGETS oisb_console RUNTIME DESTINATION bin)

# doxygen stuff
find_package(Doxygen)
if (DOXYGEN_FOUND)
	message("found doxygen, generating documentation")
	# prepare doxygen configuration file
	configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)
	# add doxygen as target
	add_custom_target(doxygen ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

	# cleanup $build/api-doc on "make clean"
	set_property(DIRECTORY APPEND PROPERTY ADDITIONAL_MAKE_CLEAN_FILES api-doc)

	# add doxygen as dependency to doc-target
	get_target_property(DOC_TARGET doc TYPE)
	if(NOT DOC_TARGET)
		add_custom_target(doc)
	endif()
	add_dependencies(doc doxygen)
	install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/doc/html/ DESTINATION doc/api)
	# install man pages into packages, scope is now project root..
	#install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/api-doc/man/man3 DESTINATION share/man/man3/ )
endif (DOXYGEN_FOUND)

# other doc files
set(DOC_FILES AUTHORS LICENSE)# NEWS README)
install(FILES ${DOC_FILES} DESTINATION doc/)


