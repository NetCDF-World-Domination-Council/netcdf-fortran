CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

SET(CMAKE_VERBOSE_MAKEFILE OFF)
SET(CMAKE_INCLUDE_CURRENT_DIR ON)

INCLUDE_DIRECTORIES("." "${NC4F_BINARY_DIR}/libsrc")

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

SET(netcdff77_SOURCES
  module_netcdf_nc_data.F90 module_netcdf_nc_interfaces.f90
  module_netcdf_nf_data.F90 module_netcdf_nf_interfaces.F90
  module_netcdf_f03.f90
  nf_attio.F90 nf_control.F90 nf_dim.f90 nf_misc.f90
  nf_genatt.f90 nf_geninq.f90 nf_genvar.f90
  nf_vario.F90 nf_var1io.F90 nf_varaio.F90
  nf_varmio.F90 nf_varsio.F90
)

IF (USE_NETCDF4)
  SET(netcdff77_SOURCES ${netcdff77_SOURCES}
    module_netcdf4_nc_interfaces.f90
    module_netcdf4_nf_interfaces.F90 module_netcdf4_f03.f90
    nf_lib.c nf_nc4.f90
    )
ENDIF(USE_NETCDF4)

IF (${USE_LOGGING})
  SET(netcdff77_SOURCES ${netcdff77_SOURCES}
    nf_logging.F90
    )
ENDIF(${USE_LOGGING})

if (${BUILD_V2})
  SET(netcdff77_SOURCES ${netcdff77_SOURCES}
    nf_v2compat.c module_netcdf_fortv2_c_interfaces.f90 nf_fortv2.f90
    )
endif (${BUILD_V2})

if (${BUILD_F03})
  SET(LCHEADERS
    netcdf_nc_data.mod netcdf_nc_interfaces.mod netcdf_nf_data.mod
    netcdf_nf_interfaces.mod netcdf_f03.mod
    )
  if (${BUILD_V2})
    SET(LCHEADERS ${LCHEADERS}
      netcdf_fortv2_c_interfaces.mod
      )
  endif (${BUILD_V2})
  if (${USE_NETCDF4})
    SET(LCHEADERS ${LCHEADERS}
      netcdf4_nc_interfaces.mod netcdf4_nf_interfaces.mod netcdf4_f03.mod
      )
  endif (${USE_NETCDF4})
endif (${BUILD_F03})

# These are always used
SET(LCCOMMON )
SET(COMMONHEADERS ${LCHEADERS} ${LCCOMMON})

SET(BUILT_SOURCES ${LCHEADERS} ${LCCOMMON})

# A macro for appending file2 to file1
MACRO(APPENDFILE file1 file2)
  file(READ ${file2} file2_bytes)
  file(APPEND ${file1} ${file2_bytes})
ENDMACRO(APPENDFILE file1 file2)

include_directories(${CMAKE_SOURCE_DIR} ${CMAKE_SOURCE_DIR}/libsrc)

# This is what we are building: a convenience library of Fortran 2003 functions.
# This just builds SHARED, even though STATIC is also specified
# ADD_LIBRARY(netcdff77 STATIC SHARED ${netcdff77_SOURCES})
# Builds only static, not shared
ADD_LIBRARY(netcdff77 OBJECT ${netcdff77_SOURCES})

SET(NETCDF_INC ${CMAKE_CURRENT_BINARY_DIR}/netcdf.inc)
SET_DIRECTORY_PROPERTIES (PROPERTIES ADDITIONAL_MAKE_CLEAN_FILES ${NETCDF_INC})
if (EXISTS ${NETCDF_INC})
  FILE (REMOVE ${NETCDF_INC})
endif ()

# Build netcdf.inc file from selected netcdf2, netcdf3 and netcdf4 apis
file(APPEND ${NETCDF_INC} "!     NetCDF-3.")
APPENDFILE(${NETCDF_INC} "netcdf3.inc")

if (${USE_NETCDF4})
  file(APPEND ${NETCDF_INC} "\n")
  file(APPEND ${NETCDF_INC} "!     NetCDF-4.")
  APPENDFILE(${NETCDF_INC} "netcdf4.inc")
endif (${USE_NETCDF4})

if (${BUILD_V2})
  file(APPEND ${NETCDF_INC} "\n")
  file(APPEND ${NETCDF_INC} "!     NetCDF-2.")
  APPENDFILE(${NETCDF_INC} "netcdf2.inc")
endif (${BUILD_V2})

if (${USE_LOGGING})
  file(APPEND ${NETCDF_INC} "\n")
  file(APPEND ${NETCDF_INC} "!     This is to turn on netCDF internal logging.")
  file(APPEND ${NETCDF_INC} "      integer nf_set_log_level")
  file(APPEND ${NETCDF_INC} "      external nf_set_log_level")
endif (${USE_LOGGING})

SET(BUILT_SOURCES ${BUILT_SOURCES} netcdf.inc)

Install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/
        DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
        FILES_MATCHING PATTERN "*.mod" PATTERN "netcdf.inc"
        )
