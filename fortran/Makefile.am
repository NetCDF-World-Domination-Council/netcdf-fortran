## This is an automake file, part of Unidata's netCDF package.
# Copyright 2005-2006, see the COPYRIGHT file for more information.

# This file controls the building of the fortran 90 API.

# Ed Hartnett, Russ Rew, Dennis Heimbigner, Ward Fisher

# This is what we are building: the netCDF fortran library, containing
# the F77 and the F90 APIs.
lib_LTLIBRARIES = libnetcdff.la

# Add the F77 API and the F90 API.
libnetcdff_la_LIBADD = ../libsrc/libnetcdff77.la libnetcdfm.la

# These linker flags specify libtool version info.
# See http://www.gnu.org/software/libtool/manual/libtool.html#Libtool-versioning
# for information regarding incrementing `-version-info`.
libnetcdff_la_LDFLAGS = -version-info 6:1:1

# This will get set below.
libnetcdff_la_SOURCES =

# These f90 codes are used for either netCDF classic or netCDF-4 F90
# API.
COMMON_CODES = netcdf_constants.f90 netcdf_externals.f90		\
netcdf_dims.f90 netcdf_attributes.f90 netcdf_overloads.f90		\
netcdf_visibility.f90 netcdf_file.f90 netcdf_variables.f90		\
netcdf_text_variables.f90 netcdf_expanded.f90 netcdf_eightbyte.f90

# These f90 codes are used for the netCDF-4 F90 API.
NETCDF4_CODES = netcdf4_externals.f90 netcdf4_visibility.f90	\
netcdf4_func.f90 netcdf4_overloads.f90 netcdf4_file.f90		\
netcdf4_eightbyte.f90 netcdf4_variables.f90

# Build these uninstalled convenience libraries.
noinst_LTLIBRARIES = libtypeSizes.la libnetcdfm.la

# Different source for the netcdf.mod is used for netcdf classic
# vs. netcdf4.
if USE_NETCDF4

# Use the netCDF-4 F90 code.
libnetcdfm_la_SOURCES = netcdf4.f90
netcdf4.$(OBJEXT): typesizes.mod
netcdf.mod: netcdf4.$(OBJEXT)
EXTRA_libnetcdfm_la_DEPENDENCIES = $(COMMON_CODES) $(NETCDF4_CODES)

else # classic-only build

# Use the netCDF classic F90 code.
libnetcdfm_la_SOURCES = netcdf.f90
netcdf.$(OBJEXT): typesizes.mod
netcdf.mod: netcdf.$(OBJEXT)
EXTRA_libnetcdfm_la_DEPENDENCIES = $(COMMON_CODES)
libnetcdff_la_SOURCES += netcdf3_file.f90

endif # USE_NETCDF4

# Each convenience library depends on its source.
libtypeSizes_la_SOURCES = typeSizes.f90

# Each mod file depends on the .o file.
typesizes.mod: typeSizes.$(OBJEXT)

# Mod files are built and then installed as headers. Order is
# significant in this list of modfiles.
MODFILES = typesizes.mod

# Add our convenience libraries to the netCDF fortran library.
libnetcdff_la_LIBADD += libtypeSizes.la

# This must come last in the list of MODFILES.
MODFILES += netcdf.mod

# Mod files are build sources.
BUILT_SOURCES = $(MODFILES)

# Mod files are installed as headers, but not distrubuted.
nodist_include_HEADERS = $(MODFILES)

EXTRA_DIST = $(COMMON_CODES) $(NETCDF4_CODES) CMakeLists.txt

CLEANFILES = *.mod
