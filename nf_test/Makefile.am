# This is a automake file, part of Unidata's netCDF package.
# Copyright 2005-2019, see the COPYRIGHT file for more information.

# This file builds and runs the f77 and f90 tests.

# Ed Hartnett, Russ Rew, Dennis Heimbigner, Ward Fisher

# Turn off parallel builds in this directory.
.NOTPARALLEL:

# CPPFLAGS is used when compiling *.F.
AM_CPPFLAGS = -I../fortran

# FCFLAGS is used when compiling *.f90
AM_FCFLAGS = -I${top_builddir}/fortran

# All tests need to link to fortran library.
LDADD = ${top_builddir}/fortran/libnetcdff.la

# nf_test is the main test program.
check_PROGRAMS = nf_test
TESTS = nf_test

nf_test_SOURCES = f03lib_f_interfaces.f90 test_get.m4 test_put.m4	\
nf_error.F nf_test.F test_read.F test_write.F util.F f03lib.c		\
tests.inc

# Did the user build the V2 F77 API? If so, run this test.
if BUILD_V2
check_PROGRAMS += tst_f77_v2
tst_f77_v2_SOURCES = tst_f77_v2.F
TESTS += tst_f77_v2
endif # BUILD_V2

# This is the fortran v2 test.
if BUILD_V2
check_PROGRAMS += ftest
TESTS += ftest
ftest_SOURCES = ftest.F f03lib.c
endif # BUILD_V2

check_PROGRAMS += tst_f90
TESTS += tst_f90
tst_f90_SOURCES = tst_f90.f90

# Tell make how to turn .m4 files into .F files.
.m4.F:
	m4 $(M4FLAGS) $< >$@

# If sed is present, generate these tests at build time. They are the
# same tests as the F77 netCDF-4 tests above, but using the "use
# netcdf4_f03" statement.
if USE_SED

# These test fortran codes will be created using sed.
F03_TEST_CODES = nf03_error.F

# The fortran codes will compile into these tests.
F03_TESTS =

if BUILD_V2
F03_TEST_CODES += f03test.F tst03_f77_v2.F
F03_TESTS += f03test tst03_f77_v2
f03test_SOURCES = f03test.F
tst03_f77_v2_SOURCES = tst03_f77_v2.F
endif # BUILD_V2

# Note that all these fortran programs are built at compile-time on
# the user system.
BUILT_SOURCES = $(F03_TEST_CODES)

# Compile and run the generated tests.
check_PROGRAMS += $(F03_TESTS)
TESTS += $(F03_TESTS)

# Build this uninstalled convenience library.
noinst_LTLIBRARIES = libtests.la
libtests_la_SOURCES = module_tests.F90
tests.mod: module_tests.$(OBJEXT)
BUILT_SOURCES += tests.mod

# Use sed to generate the f03 versions of some test codes.
f03test.F: ftest.F
	$(SED) "s|include 'netcdf.inc'|use netcdf_f03|" $< > $@.tmp
	$(SED) "s|test.nc|f03tst_test.nc|" $@.tmp > $@.tmp2
	$(SED) "s|copy.nc|f03tst_copy.nc|" $@.tmp2 > $@

tst03_f77_v2.F: tst_f77_v2.F
	$(SED) "s|IMPLICIT NONE|USE tests|" $< > $@.tmp
	$(SED) "s|include 'netcdf.inc'|implicit none|" $@.tmp > $@.tmp2
	$(SED) "s|tst_f77_v2.nc|tst03_f77_v2.nc|" $@.tmp2 > $@

nf03_error.F: nf_error.F
	$(SED) "s|implicit none|USE tests|" $< > $@.tmp
	$(SED) "s|#include \"tests.inc\"|      implicit none|" $@.tmp > $@

nf03_test.F: nf_test.F
	$(SED) "s|implicit none|USE tests|" $< > $@.tmp
	$(SED) "s|#include \"tests.inc\"|      implicit none|" $@.tmp > $@

test03_read.F: test_read.F
	$(SED) "s|implicit none|USE tests|" $< > $@.tmp
	$(SED) "s|#include \"tests.inc\"|      implicit none|" $@.tmp > $@

test03_write.F: test_write.F
	$(SED) "s|implicit none|USE tests|" $< > $@.tmp
	$(SED) "s|#include \"tests.inc\"|      implicit none|" $@.tmp > $@

util03.F: util.F
	$(SED) "s|implicit none|USE tests|" $< > $@.tmp
	$(SED) "s|#include \"tests.inc\"|      implicit none|" $@.tmp > $@

test03_put.F: test_put.F
	$(SED) "s|implicit        none|USE tests|" $< > $@.tmp
	$(SED) "s|#include \"tests.inc\"|      implicit none|" $@.tmp > $@

test03_get.F: test_get.F
	$(SED) "s|implicit        none|USE tests|" $< > $@.tmp
	$(SED) "s|#include \"tests.inc\"|      implicit none|" $@.tmp > $@

endif #USE_SED


# If valgrind is present on this machine, this will enable
# check-valgrind target, which runs all tests with valgrind.
@VALGRIND_CHECK_RULES@

# test_get.F and test_put.f need to be distributed, so that the user
# need not have m4. ref_fills.nc is copied to fills.nc by configure
# and used by test program ftest.
EXTRA_DIST = test_get.F test_put.F fills.cdl ref_fills.nc	\
CMakeLists.txt

# Cleaning up files created during the process.
CLEANFILES = scratch.nc test.nc copy.nc tst*.nc ftst_*.nc f90tst_*.nc	\
f03tst_*.nc temp.tmp fort.* *.mod $(F03_TEST_CODES)

DISTCLEANFILES = fills.nc

MAINTAINERCLEANFILES = test_get.F test_put.F
