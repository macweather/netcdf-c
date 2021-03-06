## This is a automake file, part of Unidata's netCDF package.
# Copyright 2005, see the COPYRIGHT file for more information.

# This file builds and runs the nc_test program, which tests the
# netCDF-3 API for all formats.

# Put together AM_CPPFLAGS and AM_LDFLAGS.
include $(top_srcdir)/lib_flags.am
AM_CPPFLAGS += -I$(top_srcdir)/libsrc
AM_CPPFLAGS += -DTOPSRCDIR=${abs_top_srcdir}
AM_CPPFLAGS += -DTOPBINDIR=${abs_top_bindir}
LDADD = ${top_builddir}/liblib/libnetcdf.la
AM_CPPFLAGS += -I$(top_builddir)/liblib -I$(top_builddir)/include -I$(top_srcdir)/libsrc

# Note which tests depend on other tests. necessary for make -j check
TEST_EXTENSIONS = .sh

# These are the tests which are always run.
TESTPROGRAMS = t_nc tst_small nc_test tst_misc tst_norm tst_names       \
tst_nofill tst_nofill2 tst_nofill3 tst_atts3 tst_meta tst_inq_type      \
tst_utf8_validate tst_utf8_phrases tst_global_fillval                   \
tst_max_var_dims tst_formats tst_def_var_fill

if USE_NETCDF4
TESTPROGRAMS += tst_atts tst_put_vars tst_elatefill
endif

if USE_PNETCDF
TESTPROGRAMS += tst_parallel2 tst_pnetcdf tst_addvar tst_formatx_pnetcdf
endif

if TEST_PARALLEL4
if USE_PNETCDF
if USE_CDF5
TESTPROGRAMS += tst_cdf5format
endif
endif
endif

# These are the source files for the main workhorse test program,
# nc_test. If you pass nc_test, you are doing well.
nc_test_SOURCES = nc_test.c error.c test_get.c test_put.c test_read.c	\
test_write.c util.c error.h tests.h

# If the user asked for large file tests, then add them.
if LARGE_FILE_TESTS
TESTPROGRAMS += quick_large_files tst_big_var6 tst_big_var2	\
tst_big_rvar tst_big_var tst_large large_files
endif # LARGE_FILE_TESTS

if BUILD_BENCHMARKS
TESTPROGRAMS += testnc3perf
testnc3perf_SOURCES = testnc3perf.c
endif

# Set up the tests.
check_PROGRAMS = $(TESTPROGRAMS)

# Build Diskless test helpers
if BUILD_DISKLESS
check_PROGRAMS += tst_diskless tst_diskless3 tst_diskless4 tst_diskless5
if USE_NETCDF4
check_PROGRAMS += tst_diskless2
endif
endif

TESTS = $(TESTPROGRAMS)

if BUILD_UTILITIES
if BUILD_DISKLESS
TESTS += run_diskless.sh run_diskless5.sh
if BUILD_MMAP
TESTS += run_mmap.sh
run_mmap.log: run_diskless.log
endif
if LARGE_FILE_TESTS
TESTS += run_diskless2.sh
endif
endif
endif

if USE_PNETCDF
TESTS += run_pnetcdf_test.sh
endif

# This will run a bunch of the test programs with valgrind, the memory
# checking tool. (Valgrind must be present for this to work.)
if USE_VALGRIND_TESTS
if USE_NETCDF4
    TESTS_ENVIRONMENT = USE_NETCDF4=1
else
    TESTS_ENVIRONMENT = USE_NETCDF4=0
endif
TESTS += run_valgrind_tests.sh
endif # USE_VALGRIND_TESTS

# Distribute the .c files so that m4 isn't required on the users
# machine.
EXTRA_DIST = test_get.m4 test_put.m4 run_valgrind_tests.sh		\
run_diskless.sh run_diskless2.sh run_diskless5.sh run_mmap.sh		\
run_pnetcdf_test.sh test_read.m4 test_write.m4 ref_tst_diskless2.cdl	\
tst_diskless5.cdl CMakeLists.txt

# These files are created by the tests.
CLEANFILES = nc_test_classic.nc nc_test_64bit.nc nc_test_netcdf4.nc     \
tst_*.nc t_nc.nc large_files.nc quick_large_files.nc tst_diskless.nc    \
tst_diskless2.nc tst_diskless3.nc tst_diskless3_file.cdl                \
tst_diskless3_memory.cdl tst_diskless4.cdl tst_diskless4.nc             \
tst_formatx.nc nc_test_cdf5.nc unlim.nc tst_inq_type.nc                 \
tst_elatefill.nc tst_global_fillval.nc tst_large_cdf5.nc                \
tst_max_var_dims.nc benchmark.nc tst_def_var_fill.nc

EXTRA_DIST += bad_cdf5_begin.nc run_cdf5.sh
if ENABLE_CDF5
   # bad_cdf5_begin.nc is a corrupted CDF-5 file with bad variable starting
   # file offsets. It is to be used by tst_open_cdf5.c to check if it can
   # detect and report error code NC_ENOTNC.
   TESTS          += run_cdf5.sh
   check_PROGRAMS += tst_open_cdf5
if LARGE_FILE_TESTS
   TESTPROGRAMS   += tst_large_cdf5 tst_cdf5_begin
   CLEANFILES     += tst_large_cdf5.nc tst_cdf5_begin.nc
endif
endif

# Only clean these on maintainer-clean, because they require m4 to
# regenerate.
#MAINTAINERCLEANFILES = test_get.c test_put.c

# This rule tells make how to turn our .m4 files into .c files.
.m4.c:
	m4 $(AM_M4FLAGS) $(M4FLAGS) $< >$@
