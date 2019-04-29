## This is an automake file, part of Unidata's netCDF package.
# Copyright 2005-2019, see the COPYRIGHT file for more information.

# This builds nothing any more.

# Ed Hartnett

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

EXTRA_DIST = CMakeLists.txt netcdf2.inc netcdf3.inc netcdf4.inc

CLEANFILES = netcdf.inc
