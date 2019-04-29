## This is an automake file, part of Unidata's netCDF package.
# Copyright 2005-2019, see the COPYRIGHT file for more information.

# This builds nothing any more.

# Ed Hartnett

# This is what we are building: the netCDF F77 API convenience library.
noinst_LTLIBRARIES = libnetcdf_nc_data.la libnetcdf_nf_data.la	\
libnetcdf_nc_interfaces.la libnetcdf_nf_interfaces.la		\
libnetcdf_f03.la libnetcdff77.la

# These are the source codes for the F77 API.
libnetcdff77_la_SOURCES = nf_attio.F90 nf_control.F90 nf_dim.f90	\
nf_misc.f90 nf_genatt.f90 nf_geninq.f90 nf_genvar.f90 nf_vario.F90	\
nf_var1io.F90 nf_varaio.F90 nf_varmio.F90 nf_varsio.F90

# Add nf_logging to F77 API if needed.
if USE_LOGGING
libnetcdff77_la_SOURCES += nf_logging.F90
endif USE_LOGGING

# Each convenience library depends on its source.
libnetcdf_nc_data_la_SOURCES = module_netcdf_nc_data.F90
libnetcdf_nf_data_la_SOURCES = module_netcdf_nf_data.F90
libnetcdf_nc_interfaces_la_SOURCES = module_netcdf_nc_interfaces.f90
libnetcdf_nf_interfaces_la_SOURCES = module_netcdf_nf_interfaces.F90
libnetcdf_f03_la_SOURCES = module_netcdf_f03.f90

# Each mod file depends on the .o file.
netcdf_nc_data.mod: module_netcdf_nc_data.$(OBJEXT)
netcdf_nf_data.mod: module_netcdf_nf_data.$(OBJEXT)
netcdf_nc_interfaces.mod: module_netcdf_nc_interfaces.$(OBJEXT)
netcdf_nf_interfaces.mod: module_netcdf_nf_interfaces.$(OBJEXT)
netcdf_f03.mod: module_netcdf_f03.$(OBJEXT)

# Some mods are dependant on other mods in this dir.
module_netcdf_nf_data.$(OBJEXT): netcdf_nc_data.mod
module_netcdf_nc_interfaces.$(OBJEXT): netcdf_nc_data.mod
module_netcdf_nf_interfaces.$(OBJEXT): netcdf_nf_data.mod
module_netcdf_f03.$(OBJEXT): netcdf_nf_data.mod netcdf_nf_interfaces.mod

# Mod files are built and then installed as headers. Order is
# significant in this list of modfiles.
MODFILES = netcdf_nc_data.mod netcdf_nf_data.mod	\
netcdf_nc_interfaces.mod netcdf_nf_interfaces.mod

# Add our convenience libraries to the netCDF fortran library.
libnetcdff77_la_LIBADD = libnetcdf_nc_data.la libnetcdf_nf_data.la	\
libnetcdf_nc_interfaces.la libnetcdf_nf_interfaces.la			\
libnetcdf_f03.la

# Add the V2 F77 API.
if BUILD_V2
noinst_LTLIBRARIES += libnetcdf_fortv2_c_interfaces.la
libnetcdf_fortv2_c_interfaces_la_SOURCES = module_netcdf_fortv2_c_interfaces.f90
netcdf_fortv2_c_interfaces.mod: module_netcdf_fortv2_c_interfaces.$(OBJEXT)
module_netcdf_fortv2_c_interfaces.$(OBJEXT): netcdf_nc_interfaces.mod
MODFILES += netcdf_fortv2_c_interfaces.mod
libnetcdff77_la_SOURCES += nf_v2compat.c nf_fortv2.f90
libnetcdff77_la_LIBADD += libnetcdf_fortv2_c_interfaces.la
endif # BUILD_V2

# The netcdf.inc file is built on the user machine, and installed as
# a header.
BUILT_SOURCES = netcdf.inc
nodist_include_HEADERS = netcdf.inc

# Build netcdf.inc file from netcdf2, netcdf3 and netcdf4 files
netcdf.inc: netcdf2.inc netcdf3.inc netcdf4.inc
	echo '!     NetCDF-3.' > netcdf.inc
	cat $(top_srcdir)/libsrc/netcdf3.inc >> netcdf.inc
if USE_NETCDF4
	echo >> netcdf.inc
	echo '!     NetCDF-4.' >> netcdf.inc
	cat $(top_srcdir)/libsrc/netcdf4.inc >> netcdf.inc
endif
if BUILD_V2
	echo >> netcdf.inc
	echo '!     NetCDF-2.' >> netcdf.inc
	cat $(top_srcdir)/libsrc/netcdf2.inc >> netcdf.inc
endif
if USE_LOGGING
	echo >> netcdf.inc
	echo '!     This is to turn on netCDF internal logging.' >> netcdf.inc
	echo '      integer nf_set_log_level' >> netcdf.inc
	echo '      external nf_set_log_level' >> netcdf.inc
endif

# Mod files are build sources.
BUILT_SOURCES += $(MODFILES)

# Mod files are installed as headers, but not distrubuted.
nodist_include_HEADERS += $(MODFILES)

EXTRA_DIST = CMakeLists.txt netcdf2.inc netcdf3.inc netcdf4.inc

CLEANFILES = netcdf.inc *.mod
