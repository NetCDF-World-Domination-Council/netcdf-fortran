CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

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
