CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

SET(CMAKE_VERBOSE_MAKEFILE OFF)
SET(CMAKE_INCLUDE_CURRENT_DIR ON)

INCLUDE_DIRECTORIES("." "${NC4F_BINARY_DIR}/libsrc")
LINK_DIRECTORIES("." "${NC4F_BINARY_DIR}/libsrc")

SET(netcdff_SOURCES
  typeSizes.f90
)

IF (${USE_NETCDF4})
  SET(netcdff_SOURCES ${netcdff_SOURCES}
    netcdf4.f90
    )
ELSE ()
  SET(netcdff_SOURCES ${netcdff_SOURCES}
    netcdf.f90 netcdf3_file.f90
    )
ENDIF(${USE_NETCDF4})

# These are always used
SET(LCCOMMON typesizes.mod netcdf.mod)
SET(COMMONHEADERS ${LCHEADERS} ${LCCOMMON})

SET(BUILT_SOURCES ${LCHEADERS} ${LCCOMMON})

include_directories(${CMAKE_SOURCE_DIR} ${CMAKE_SOURCE_DIR}/libsrc)

# Default to shared libs on
OPTION(BUILD_SHARED_LIBS "Configure netCDF as a shared library." ON)
SET (LIB_TYPE STATIC)
IF (BUILD_SHARED_LIBS)
  SET(LIB_TYPE SHARED)
  IF(CMAKE_COMPILER_IS_GNUCC)
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
  ENDIF()
ENDIF()

SET(netcdff_LIBS ${netcdff_LIBS} netcdff77)

# This is what we are building: a convenience library of Fortran 2003 functions.
# This just builds SHARED, even though STATIC is also specified
# ADD_LIBRARY(netcdff STATIC SHARED ${netcdff_SOURCES})
# Builds only static, not shared
ADD_LIBRARY(netcdff ${netcdff_SOURCES})
TARGET_LINK_LIBRARIES(netcdff ${NETCDF_C_LIBRARY})
TARGET_LINK_LIBRARIES(netcdff ${EXTRA_DEPS})
SET_TARGET_PROPERTIES(netcdff PROPERTIES
                      VERSION ${NC4F_LIB_VERSION}
                      SOVERSION ${NC4F_SO_VERSION}
                      )

##
# Configuration for post-install RPath
# Adapted from http://www.cmake.org/Wiki/CMake_RPATH_handling
##
IF(NOT MSVC)
  # use, i.e. don't skip the full RPATH for the build tree
  SET(CMAKE_SKIP_BUILD_RPATH  FALSE)

  # when building, don't use the install RPATH already
  # (but later on when installing)
  SET(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)

  SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")

  # add the automatically determined parts of the RPATH
  # which point to directories outside the build tree to the install RPATH
  SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

  # the RPATH to be used when installing, but only if it's not a system directory
  LIST(FIND CMAKE_PLATFORM_IMPLICIT_LINK_DIRECTORIES "${CMAKE_INSTALL_PREFIX}/lib" isSystemDir)
  IF("${isSystemDir}" STREQUAL "-1")
    SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
  ENDIF("${isSystemDir}" STREQUAL "-1")
ENDIF()

# Installation of the program
INSTALL(TARGETS netcdff
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}" COMPONENT bin
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}" COMPONENT shlib
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}" COMPONENT shlib
    )
INSTALL(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/
        DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
        FILES_MATCHING PATTERN "*.mod" PATTERN "netcdf.inc"
        )
