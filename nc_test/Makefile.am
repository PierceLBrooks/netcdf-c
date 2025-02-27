## This is a automake file, part of Unidata's netCDF package.
# Copyright 2018, see the COPYRIGHT file for more information.

# This file builds and runs the nc_test program, which tests the
# netCDF-3 API for all formats.

# Ed Hartnett, Dennis Heimbigner, Ward Fisher

# Un comment to use a more verbose test driver
#SH_LOG_DRIVER = $(SHELL) $(top_srcdir)/test-driver-verbose
#sh_LOG_DRIVER = $(SHELL) $(top_srcdir)/test-driver-verbose
#LOG_DRIVER = $(SHELL) $(top_srcdir)/test-driver-verbose
#TESTS_ENVIRONMENT = export SETX=1;

# Put together AM_CPPFLAGS and AM_LDFLAGS.
include $(top_srcdir)/lib_flags.am
AM_CPPFLAGS += -I$(top_srcdir)/libsrc
AM_CPPFLAGS += -DTOPSRCDIR=${abs_top_srcdir}
AM_CPPFLAGS += -DTOPBINDIR=${abs_top_builddir}
LDADD = ${top_builddir}/liblib/libnetcdf.la
AM_CPPFLAGS += -I$(top_builddir)/liblib -I$(top_builddir)/include -I$(top_srcdir)/libsrc

TEST_EXTENSIONS = .sh

# These are the tests which are always run.
TESTPROGRAMS = tst_names tst_nofill2 tst_nofill3 tst_meta		\
tst_inq_type tst_utf8_validate tst_utf8_phrases tst_global_fillval	\
tst_max_var_dims tst_formats tst_def_var_fill tst_err_enddef		\
tst_default_format

# These are always built, but for parallel builds are run from a test
# script, because they are parallel-enabled tests.
check_PROGRAMS = t_nc tst_atts3 tst_nofill nc_test tst_small

# These tests are only run if pnetcdf is enabled.
if USE_PNETCDF
check_PROGRAMS += tst_parallel2 tst_pnetcdf tst_addvar	\
tst_formatx_pnetcdf tst_default_format_pnetcdf
endif

if USE_PNETCDF
if ENABLE_CDF5
check_PROGRAMS += tst_cdf5format
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
endif

if USE_HDF5
TESTPROGRAMS += tst_diskless6
endif

# Set up the tests.
check_PROGRAMS += $(TESTPROGRAMS)

# Build Diskless test helpers
check_PROGRAMS += tst_diskless tst_diskless3 tst_diskless4 \
tst_diskless5 tst_inmemory tst_open_mem
if USE_HDF5
check_PROGRAMS += tst_diskless2
endif

TESTS = $(TESTPROGRAMS)

if BUILD_UTILITIES

if ENABLE_BYTERANGE
if ENABLE_EXTERNAL_SERVER_TESTS
tst_byterange_SOURCES = tst_byterange.c
check_PROGRAMS += tst_byterange
TESTS += test_byterange.sh
endif
endif

TESTS += run_diskless.sh run_diskless5.sh run_inmemory.sh
if LARGE_FILE_TESTS
if ! ENABLE_PARALLEL
TESTS += run_diskless2.sh
endif
endif

if BUILD_MMAP
TESTS += run_mmap.sh
run_mmap.log: run_diskless.log
endif

# If pnetcdf is enabled, these tests are run by a test
# script. Otherwise, the are run by automake in the usual way.
if USE_PNETCDF
TESTS += run_pnetcdf_tests.sh
else
TESTS += t_nc tst_atts3 tst_nofill nc_test tst_small
endif

endif # BUILD_UTILITIES

# The .c files that are generated with m4 are already distributed, but
# we also include the original m4 files, plus test scripts data.
EXTRA_DIST = test_get.m4 test_put.m4 run_diskless.sh run_diskless2.sh	\
run_diskless5.sh run_mmap.sh test_read.m4		\
test_write.m4 ref_tst_diskless2.cdl tst_diskless5.cdl			\
ref_tst_diskless3_create.cdl ref_tst_diskless3_open.cdl			\
run_inmemory.sh run_mmap.sh f03tst_open_mem.nc test_byterange.sh	\
ref_tst_http_nc3.cdl ref_tst_http_nc4a.cdl ref_tst_http_nc4b.cdl	\
ref_tst_http_nc4c.cdl CMakeLists.txt run_pnetcdf_tests.sh.in \
tst_misc.c tst_norm.c

# These files are created by the tests.
CLEANFILES = nc_test_*.nc tst_*.nc t_nc.nc large_files.nc		\
quick_large_files.nc tst_diskless3_file.cdl                             \
tst_diskless4.cdl ref_tst_diskless4.cdl benchmark.nc                    \
tst_http_nc3.cdl tst_http_nc4?.cdl tmp*.cdl tmp*.nc

EXTRA_DIST += bad_cdf5_begin.nc run_cdf5.sh nc_enddef.cdl
if ENABLE_CDF5
   # bad_cdf5_begin.nc is a corrupted CDF-5 file with bad variable starting
   # file offsets. It is to be used by tst_open_cdf5.c to check if it can
   # detect and report error code NC_ENOTNC.
   TESTS          += run_cdf5.sh
   check_PROGRAMS += tst_open_cdf5
if LARGE_FILE_TESTS
   TESTPROGRAMS   += tst_large_cdf5 tst_cdf5_begin
endif
endif

# Only clean these on maintainer-clean, because they require m4 to
# regenerate.
MAINTAINERCLEANFILES = test_get.c test_put.c

# This rule tells make how to turn our .m4 files into .c files.
.m4.c:
	m4 $(AM_M4FLAGS) $(M4FLAGS) $< >$@

# If valgrind is present, add valgrind targets.
@VALGRIND_CHECK_RULES@
