cmake_minimum_required(VERSION 2.4.6)

project(python_orocos_kdl_python3)

find_package(orocos_kdl)
include_directories(${orocos_kdl_INCLUDE_DIRS})
link_directories(${orocos_kdl_LIBRARY_DIRS})

set(Python_ADDITIONAL_VERSIONS 3.6;3.7;3.8) ## https://stackoverflow.com/questions/29245558/how-to-link-with-python3-libs-with-cmake
find_package(PythonInterp 3 REQUIRED) ## cv_bridge_python3 :: use Python3 by default
find_package(PythonLibs "${PYTHON_VERSION_MAJOR}.${PYTHON_VERSION_MINOR}")
execute_process(COMMAND ${PYTHON_EXECUTABLE} -c "from distutils.sysconfig import get_python_lib; print(get_python_lib(plat_specific=True, prefix=''))" OUTPUT_VARIABLE PYTHON_SITE_PACKAGES OUTPUT_STRIP_TRAILING_WHITESPACE)
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
find_package(SIP REQUIRED)
include(SIPMacros)

include_directories(${SIP_INCLUDE_DIR} ${PYTHON_INCLUDE_DIRS})

file(GLOB SIP_FILES "${CMAKE_CURRENT_SOURCE_DIR}/*.sip")
set(SIP_INCLUDES ${SIP_FILES})
set(SIP_EXTRA_OPTIONS "-o")
set(PYTHON_SITE_PACKAGES_INSTALL_DIR ${CMAKE_INSTALL_PREFIX}/${PYTHON_SITE_PACKAGES})
add_sip_python_module(PyKDL PyKDL/PyKDL.sip ${orocos_kdl_LIBRARIES})

# install(FILES package.xml DESTINATION share/python_orocos_kdl)

## python_orocos_kdl_pytyhon3 :: re-set PROJECT_NAME to python_orocos_kdl
set(PROJECT_NAME python_orocos_kdl)
## python_orocos_kdl_pytyhon3 :: re-set CATKIN_PACKAGE_PYTHON_DESTINATION with PROJECT_NAME as python_orocos_kdl
set(CATKIN_PACKAGE_PYTHON_DESTINATION lib/python${PYTHON_VERSION_MAJOR}/dist-packages/${PROJECT_NAME})
