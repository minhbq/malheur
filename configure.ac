# MALHEUR - Automatic Analysis of Malware Behavior
# Copyright (c) 2009-2012 Konrad Rieck (konrad@mlsec.org)          
# University of Goettingen, Berlin Institute of Technology
#
 
# Malheur version
AC_INIT([malheur], [0.5.4], [konrad@mlsec.org])
AC_PREFIX_DEFAULT("/usr/local")

# Defines (suitable for loading and saving versioned files)
AC_DEFINE(MALHEUR_MAJOR, 0, Major version number)
AC_DEFINE(MALHEUR_MINOR, 5, Minor version number)
AC_DEFINE(MALHEUR_PATCH, 4, Patch version number)
AC_CONFIG_HEADER([config.h])

echo
echo " > MALHEUR - Automatic Analysis of Malware Behavior"
echo "   Copyright (c) 2009-2013 Konrad Rieck (konrad@mlsec.org)"
echo "   University of Goettingen, Berlin Institute of Technology"
echo

# Init automake 
AM_INIT_AUTOMAKE

# Check for important programs
AC_PROG_CC
AC_PROG_LD
AC_PROG_INSTALL

# Libtool stuff
AC_CONFIG_MACRO_DIR(m4)
AC_PROG_LIBTOOL

# Set GCC and C99 flags if present
if test "$GCC" = "yes" ; then
   CFLAGS="$CFLAGS -std=c99 -Wall -fPIC"
fi

AC_ARG_ENABLE(openmp, 
    [  --enable-openmp         enable support for OpenMP (experimental) ],
    [ 
	if test "x$enableval" = "xyes" ; then
		MALHEUR_OPENMP=yes
		AC_DEFINE(MALHEUR_OPENMP, 1, [Enable support for OpenMP (experimental)])
        else
		MALHEUR_OPENMP=no
        fi
    ], [ MALHEUR_OPENMP=no ]
)

# OpenMP support
AX_OPENMP([
   HAVE_OPENMP=yes
   AC_DEFINE(HAVE_OPENMP, 1, [Enable OpenMP support])
],[
   HAVE_OPENMP=no
])

if test "x$MALHEUR_OPENMP" = "xyes" ; then
   if test "x$HAVE_OPENMP" = "xno" ; then
      AC_MSG_ERROR([OpenMP support enabled but OpenMP not available])
   fi
   CFLAGS="$CFLAGS $OPENMP_CFLAGS"
fi 

# uthash
AC_CHECK_HEADERS([uthash.h uthash/uthash.h], HEADER_UTHASH="yes")
#if test "x$HEADER_UTHASH" = "x" ; then
#    AC_MSG_ERROR([The header uthash.h is required for compilation])
#fi

# libconfig
PKG_CHECK_MODULES([PKGCONFIG], [libconfig >= 1.4])
CFLAGS="$CFLAGS $PKGCONFIG_CFLAGS"
LIBS="$LIBS $PKGCONFIG_LIBS"
AM_CPPFLAGS="$AM_CPPFLAGS `pkg-config --cflags-only-I libconfig`"
AC_CHECK_HEADERS([libconfig.h], HEADER_LIBCONFIG="yes")

# libm
AC_CHECK_HEADERS([math.h], HEADER_MATH="yes")
AC_CHECK_LIB([m], pow, LIBRARY_MATH="yes")
if test "x$LIBRARY_MATH" != "x" && test "x$HEADER_MATH" != "x" ; then
    LIBS="-lm $LIBS"    
else
    AC_MSG_ERROR([The library libm is required for compilation])
fi

# libz 
AC_CHECK_HEADERS([zlib.h], HEADER_ZLIB="yes")
AC_CHECK_LIB([z], gzopen, LIBRARY_ZLIB="yes")
if test "x$LIBRARY_ZLIB" != "x" && test "x$HEADER_ZLIB" != "x" ; then
    LIBS="-lz $LIBS"    
else
    AC_MSG_ERROR([The library libz is required for compilation])
fi

# libarchive
AC_CHECK_HEADERS([archive.h], HEADER_LIBARCHIVE="yes")
AC_CHECK_LIB([archive], archive_read_new, LIBRARY_LIBARCHIVE="yes")
if test "x$LIBRARY_LIBARCHIVE" != "x" && test "x$HEADER_LIBARCHIVE" != "x" ; then
    LIBS="-larchive $LIBS"    
else
    AC_MSG_ERROR([The library libarchive is required for compilation])
fi


# Check headers
AC_CHECK_HEADERS([getopt.h string.h strings.h])

# Check functions
AC_CHECK_FUNC(round, AC_DEFINE(HAVE_FUNC_ROUND, 1, 
       [Define to 1 if you have the function round]))
AC_CHECK_FUNC(log2, AC_DEFINE(HAVE_FUNC_LOG2, 1, 
       [Define to 1 if you have the function log2]))

# Check matlab support
AC_ARG_ENABLE(matlab,
    [  --enable-matlab         enable optional Matlab tools],
    [ 
        if test "x$enableval" = "xyes" ; then
                MATLAB_TOOLS=yes
        else
                MATLAB_TOOLS=no
        fi
    ], [ MATLAB_TOOLS=no ]
)
if test $MATLAB_TOOLS = yes ; then
   AX_MATLAB   
   AX_MATLAB_ARCH
   AX_MATLAB_VERSION
   AX_MATLAB_DIR
   AX_MEX_OPTIONS
   AX_PATH_MEX
   AX_MEXEXT
   if test x$ax_enable_matlab = xyes ; then
      HAVE_MATLAB="yes"
   else
      HAVE_MATLAB="no"
   fi
fi

if test "x$MATLAB_TOOLS" = "xyes" && test "x$HAVE_MATLAB" = "xno" ; then
   AC_MSG_ERROR([Matlab tools enabled but Matlab not available])
fi 
AM_CONDITIONAL(MATLAB_TOOLS, test "x$MATLAB_TOOLS" = "xyes")

AC_SUBST([AM_CPPFLAGS])
AC_CONFIG_FILES([ 
   Makefile \
   src/Makefile \
   doc/Makefile \
   matlab/Makefile \
   tests/Makefile \
])

AC_OUTPUT_COMMANDS([],[
   MALHEUR_OPENMP=$MALHEUR_OPENMP
])

AC_OUTPUT

echo
echo " > Environment"
echo "   CC:              '$CC'"
echo "   CFLAGS:          '$CFLAGS'"
echo "   LD:              '$LD'"
echo "   LDFLAGS:         '$LDFLAGS'"
echo " > Features"
echo "   Matlab tools:    $MATLAB_TOOLS "
echo "   OpenMP support:  $MALHEUR_OPENMP "
