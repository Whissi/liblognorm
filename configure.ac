                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ(2.61)
AC_INIT([liblognorm], [1.1.0], [rgerhards@adiscon.com])
AM_INIT_AUTOMAKE
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])
AC_CONFIG_SRCDIR([src/lognorm.c])
AC_CONFIG_HEADER([config.h])

# Checks for programs.
AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_CC_C99
if test "$GCC" = "yes"
then CFLAGS="$CFLAGS -W -Wall -Wformat-security -Wshadow -Wcast-align -Wpointer-arith -Wmissing-format-attribute -g"
fi
AC_PROG_LIBTOOL

# Checks for libraries.
save_LIBS=$LIBS
LIBS=
AC_SEARCH_LIBS(clock_gettime, rt)
LIBS=$save_LIBS

# Checks for header files.
AC_HEADER_STDC
#AC_CHECK_HEADERS([])

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
#AC_HEADER_TIME
#AC_STRUCT_TM

# Checks for library functions.
AC_FUNC_MALLOC
AC_FUNC_SELECT_ARGTYPES
AC_TYPE_SIGNAL
#AC_CHECK_FUNCS([])

LIBLOGNORM_CFLAGS="-I\$(top_srcdir)/include"
LIBLOGNORM_LIBS="\$(top_builddir)/src/liblognorm.la"
AC_SUBST(LIBLOGNORM_CFLAGS)
AC_SUBST(LIBLOGNORM_LIBS)

# modules we require
PKG_CHECK_MODULES(LIBESTR, libestr >= 0.0.0)
PKG_CHECK_MODULES(JSON_C, json-c,, [PKG_CHECK_MODULES(JSON_C, json)])

CFLAGS_BEFORE="$CFLAGS"
LIBS_BEFORE="$LIBS"
CFLAGS="$CFLAGS $JSON_C_CFLAGS"
LIBS="$LIBS $JSON_C_LIBS"
AC_CHECK_FUNCS(json_object_object_get_ex,,)
AC_CHECK_FUNCS(json_object_get_string_len,,)
LIBS="$LIBS_BEFORE"
CFLAGS="$CFLAGS_BEFORE"

# Regular expressions
AC_ARG_ENABLE(regexp,
        [AS_HELP_STRING([--enable-regexp],[Enable regular expressions support @<:@default=no@:>@])],
        [case "${enableval}" in
         yes) enable_regexp="yes" ;;
          no) enable_regexp="no" ;;
           *) AC_MSG_ERROR(bad value ${enableval} for --enable-regexp) ;;
         esac],
        [enable_regexp="no"]
)
AM_CONDITIONAL(ENABLE_REGEXP, test x$enable_regexp = xyes)
if test "$enable_regexp" = "yes"; then
        PKG_CHECK_MODULES(PCRE, libpcre)
        AC_DEFINE(FEATURE_REGEXP, 1, [Regular expressions support enabled.])
	FEATURE_REGEXP=1
else
	FEATURE_REGEXP=0
fi
AC_SUBST(FEATURE_REGEXP)

# debug mode settings
AC_ARG_ENABLE(debug,
        [AS_HELP_STRING([--enable-debug],[Enable debug mode @<:@default=no@:>@])],
        [case "${enableval}" in
         yes) enable_debug="yes" ;;
          no) enable_debug="no" ;;
           *) AC_MSG_ERROR(bad value ${enableval} for --enable-debug) ;;
         esac],
        [enable_debug="no"]
)
if test "$enable_debug" = "yes"; then
        AC_DEFINE(DEBUG, 1, [Defined if debug mode is enabled (it's easier to check in the code).])
fi
if test "$enable_debug" = "no"; then
        AC_DEFINE(NDEBUG, 1, [Defined if debug mode is disabled.])
fi

# docs (html) build settings
AC_ARG_ENABLE(docs,
	[AS_HELP_STRING([--disable-docs],[Disable building HTML docs (requires Sphinx)])],
	[enable_docs="no"],
	[enable_docs="yes"]
)
AC_CHECK_PROGS([SPHINXBUILD], [sphinx-build sphinx-build3 sphinx-build2], [no])
AS_IF([test "$enable_docs" = "yes" -a "x$SPHINXBUILD" = xno],
    [AC_MSG_ERROR([sphinx-build is required to build documentation, install it or try --disable-docs])]
)
AM_CONDITIONAL([ENABLE_DOCS], [test "$enable_docs" = yes])

AC_ARG_ENABLE(testbench,
        [AS_HELP_STRING([--enable-testbench],[testbench enabled @<:@default=no@:>@])],
        [case "${enableval}" in
         yes) enable_testbench="yes" ;;
          no) enable_testbench="no" ;;
           *) AC_MSG_ERROR(bad value ${enableval} for --enable-testbench) ;;
         esac],
        [enable_testbench=no]
)
AM_CONDITIONAL(ENABLE_TESTBENCH, test x$enable_testbench = xyes)

AC_ARG_ENABLE(valgrind,
        [AS_HELP_STRING([--enable-valgrind],[valgrind enabled @<:@default=no@:>@])],
        [case "${enableval}" in
         yes) enable_valgrind="yes" ;;
          no) enable_valgrind=="no" ;;
           *) AC_MSG_ERROR(bad value ${enableval} for --enable-valgrind) ;;
         esac],
        [enable_valgrind=no]
)
AM_CONDITIONAL(ENABLE_VALGRIND, test x$enable_valgrind = xyes)
VALGRIND="$enable_valgrind"
AC_SUBST(VALGRIND)

AC_CONFIG_FILES([Makefile \
		lognorm.pc \
		doc/Makefile \
		src/Makefile \
		src/lognorm-features.h \
		tests/Makefile \
		tests/options.sh])
AC_OUTPUT
AC_CONFIG_MACRO_DIR([m4])


echo "*****************************************************"
echo "liblognorm will be compiled with the following settings:"
echo
echo "Regex enabled:               $enable_regexp"
echo "Testbench enabled:           $enable_testbench"
echo "Valgrind enabled:            $enable_valgrind"
echo "Debug mode enabled:          $enable_debug"
echo "Docs enabled:                $enable_docs"

