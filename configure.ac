#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.68])
AC_INIT([fomosto-psgrn-pscmp], [2008a])
AM_INIT_AUTOMAKE([-Wall -Werror foreign])
AC_PROG_F77
AC_CANONICAL_HOST
AC_CONFIG_FILES([
                 Makefile
                 src/psgrn/Makefile
                 src/pscmp/Makefile
                 ])

case $host_os in
  linux*) AM_FFLAGS="-mcmodel=medium" ;;
  *)      AM_FFLAGS="" ;;
esac
AC_SUBST([AM_FFLAGS])
AC_OUTPUT
