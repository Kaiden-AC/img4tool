AC_PREREQ([2.69])
AC_INIT([img4tool], m4_esyscmd([git rev-list --count HEAD | tr -d '\n']), [tihmstar@gmail.com])

AC_CANONICAL_SYSTEM
AC_CANONICAL_HOST
AM_PROG_LIBTOOL

AM_INIT_AUTOMAKE([subdir-objects])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIRS([m4])


AC_DEFINE([VERSION_COMMIT_COUNT], "m4_esyscmd([git rev-list --count HEAD | tr -d '\n'])", [Git commit count])
AC_DEFINE([VERSION_COMMIT_SHA], "m4_esyscmd([git rev-parse HEAD | tr -d '\n'])", [Git commit sha])
AC_SUBST([VERSION_COMMIT_COUNT], ["m4_esyscmd([git rev-list --count HEAD | tr -d '\n'])"])
AC_SUBST([VERSION_COMMIT_SHA], ["m4_esyscmd([git rev-parse HEAD | tr -d '\n'])"])

# Checks for programs.
AC_PROG_CXX
AC_PROG_CC

crypto_backend="none"
# Check for operating system
AC_MSG_CHECKING([whether we need platform-specific build settings])
case $host_os in
  darwin* )
  AC_DEFINE(HAVE_COMMCRYPTO, 1, [Define if you have CommonCrypto (macOS)])
  crypto_backend="CommCrypto"
  CXXFLAGS+=" -stdlib=libc++"
        ;;
  *mingw32*|*cygwin*|*msys*)
  AC_MSG_RESULT([${host_os}])
  win32=true
      ;;
esac

CXXFLAGS+=" -std=c++11"
CFLAGS+=" -std=c11"
# Versioning.

# Checks for libraries.
# libgeneral + libfwkeyfetch + libfragmentzip + libjssy are vendored as git
# submodules under external/ and built statically, so no system pkg-config
# install is required for them.
# (https://github.com/tihmstar/libgeneral,
#  https://github.com/Kaiden-AC/libfwkeyfetch,
#  https://github.com/tihmstar/libfragmentzip,
#  https://github.com/tihmstar/jssy.git)
# Standardized libs (libplist, openssl, libcurl, zlib) remain via pkg-config.
LIBPLIST_REQUIRES_STR="libplist-2.0 >= 2.3.0"
OPENSSL_REQUIRES_STR="openssl >= 0.9.8"
LIBCURL_REQUIRES_STR="libcurl >= 8.5.0"
LIBZ_REQUIRES_STR="zlib >= 1.0"

PKG_CHECK_MODULES(libplist, $LIBPLIST_REQUIRES_STR, have_plist=yes, have_plist=no)
PKG_CHECK_MODULES(openssl, $OPENSSL_REQUIRES_STR, have_openssl=yes, have_openssl=no)
PKG_CHECK_MODULES(libcurl, $LIBCURL_REQUIRES_STR)
PKG_CHECK_MODULES(zlib, $LIBZ_REQUIRES_STR)

AC_ARG_ENABLE([debug],
            [AS_HELP_STRING([--enable-debug],
            [enable debug build(default is no)])],
            [debug_build=true],
            [debug_build=false])

AC_ARG_ENABLE([separttool],
            [AS_HELP_STRING([--enable-separttool],
            [enable building separttool binary(default is no)])],
            [enable_separttool=true],
            [enable_separttool=false])

AC_ARG_WITH([libfwkeyfetch],
            [AS_HELP_STRING([--without-libfwkeyfetch],
            [do not build with libfwkeyfetch (vendored, default is yes) @<:@default=yes@:>@])],
            [with_libfwkeyfetch=no],
            [with_libfwkeyfetch=yes])

AC_ARG_WITH([plist],
            [AS_HELP_STRING([--without-plist],
            [do not build with libplist @<:@default=yes@:>@])],
            [with_plist=no],
            [with_plist=yes])

AC_ARG_WITH([openssl],
            [AS_HELP_STRING([--without-openssl],
            [do not build with openssl @<:@default=yes@:>@])],
            [with_openssl=no],
            [with_openssl=yes])

AC_ARG_WITH([compression],
            [AS_HELP_STRING([--without-compression],
            [do not build with bvx2 support @<:@default=yes@:>@])],
            [with_compression=no],
            [with_compression=yes])

AM_CONDITIONAL(ENABLE_SEPARTTOOL, test x$enable_separttool == xtrue)

# libfwkeyfetch is vendored, so it is always available when requested.
if test "x$with_libfwkeyfetch" == "xyes"; then
  AC_DEFINE(HAVE_LIBFWKEYFETCH, 1, [Define if you have libfwkeyfetch])
  AC_SUBST([HEADER_HAVE_LIBFWKEYFETCH], [1])
else
  echo "*** Note: libfwkeyfetch has been disabled ***"
  AC_SUBST([HEADER_HAVE_LIBFWKEYFETCH], [0])
fi

if test "x$with_plist" == "xyes"; then
  if test "x$have_plist" = "xyes"; then
    AC_DEFINE(HAVE_PLIST, 1, [Define if you have libplist])
    AC_SUBST([HEADER_HAVE_PLIST], [1])
    AC_SUBST([libplist_requires], [$LIBPLIST_REQUIRES_STR])
    AC_SUBST(libplist_CFLAGS)
    AC_SUBST(libplist_LIBS)
  else
    AC_MSG_ERROR([requested building with libplist, but library could not be found])
  fi
else
  echo "*** Note: libplist has been disabled ***"
  AC_SUBST([HEADER_HAVE_PLIST], [0])
fi

if test "x$with_openssl" == "xyes"; then
  if test "x$have_openssl" = "xyes"; then
    AC_DEFINE(HAVE_OPENSSL, 1, [Define if you have openssl])
    AC_SUBST([HEADER_HAVE_OPENSSL], [1])
    AC_SUBST([openssl_requires], [$OPENSSL_REQUIRES_STR])
    AC_SUBST(openssl_CFLAGS)
    AC_SUBST(openssl_LIBS)
    crypto_backend="openssl"
  else
    AC_MSG_ERROR([requested building with openssl, but library could not be found])
  fi
else
  echo "*** Note: openssl has been disabled ***"
  AC_SUBST([HEADER_HAVE_OPENSSL], [0])
fi

if test "x$crypto_backend" != "xnone"; then
  AC_DEFINE(HAVE_CRYPTO, 1, [Define if you have either openssl or CommCrypto])
  AC_SUBST([HEADER_HAVE_CRYPTO], [1])
else
  AC_SUBST([HEADER_HAVE_CRYPTO], [0])
fi

if test "$debug_build" = true; then
  echo "*** Note: debug build requested ***"
  CFLAGS+=" -g -O0 -DDEBUG=1"
  CXXFLAGS+=" -g -O0 -DDEBUG=1"
fi

# Checks for header files.
# fcntl.h/sys/stat.h/unistd.h are required by vendored libgeneral (Utils.cpp
# readFile/writeFile) — without HAVE_FCNTL_H it throws
# "Compiled without file support".
AC_CHECK_HEADERS([arpa/inet.h byteswap.h endian.h fcntl.h stdint.h stdlib.h string.h sys/param.h sys/stat.h unistd.h winsock.h])

# libgeneral headers are generated from .in files (vendored submodule).
# ByteOrder.hpp needs the following substitutions (mirrors libgeneral's
# own configure.ac); undeclared fall back to __builtin_bswap in the header.
if test "x$ac_cv_header_arpa_inet_h" = "xyes"; then
  AC_SUBST([HEADER_HAVE_ARPA_INET_H], [1])
else
  AC_SUBST([HEADER_HAVE_ARPA_INET_H], [0])
fi
if test "x$ac_cv_header_byteswap_h" = "xyes"; then
  AC_SUBST([HEADER_HAVE_BYTESWAP_H], [1])
else
  AC_SUBST([HEADER_HAVE_BYTESWAP_H], [0])
fi
if test "x$ac_cv_header_endian_h" = "xyes"; then
  AC_SUBST([HEADER_HAVE_ENDIAN_H], [1])
else
  AC_SUBST([HEADER_HAVE_ENDIAN_H], [0])
fi
if test "x$ac_cv_header_winsock_h" = "xyes"; then
  AC_SUBST([HEADER_HAVE_WINSOCK_H], [1])
else
  AC_SUBST([HEADER_HAVE_WINSOCK_H], [0])
fi
AC_CHECK_DECLS([htonll], [AC_SUBST([HAVE_DECL_HTONLL], [1])], [AC_SUBST([HAVE_DECL_HTONLL], [0])], [[#include <arpa/inet.h>]])
AC_CHECK_DECLS([htole64],  [AC_SUBST([HAVE_DECL_HTOLE64], [1])],  [AC_SUBST([HAVE_DECL_HTOLE64], [0])],  [[#include <endian.h>]])
AC_CHECK_DECLS([htole32],  [AC_SUBST([HAVE_DECL_HTOLE32], [1])],  [AC_SUBST([HAVE_DECL_HTOLE32], [0])],  [[#include <endian.h>]])
AC_CHECK_DECLS([htole16],  [AC_SUBST([HAVE_DECL_HTOLE16], [1])],  [AC_SUBST([HAVE_DECL_HTOLE16], [0])],  [[#include <endian.h>]])
AC_CHECK_DECLS([htons], [AC_SUBST([HAVE_DECL_HTONS], [1])], [AC_SUBST([HAVE_DECL_HTONS], [0])], [[#include <arpa/inet.h>]])
AC_CHECK_DECLS([htonl], [AC_SUBST([HAVE_DECL_HTONL], [1])], [AC_SUBST([HAVE_DECL_HTONL], [0])], [[#include <arpa/inet.h>]])
AC_SUBST([LIBGENERAL_CLEANUP_IS_DISABLED], [0])

# Check for libraries
if test "x$with_compression" == "xyes"; then
  AC_CHECK_LIB([compression], [compression_decode_buffer], [], [
    AC_CHECK_LIB([lzfse], [lzfse_decode_buffer], [], [
      echo "Error! Either libcompression or liblzfse is needed!"
      exit -1
    ])
  ])
else
  echo "*** Note: bvx2 has been disabled ***"
fi

if test "x$win32" == "xtrue"; then
  #this needs to be done after AC_CHECK_LIB, because -no-undefined breaks that!
  LDFLAGS+=" -no-undefined -lws2_32"
fi

# Checks for typedefs, structures, and compiler characteristics.
AC_CHECK_HEADER_STDBOOL
AC_TYPE_SIZE_T
AC_TYPE_UINT32_T
AC_TYPE_UINT64_T
AC_TYPE_UINT8_T

AC_CHECK_FUNCS([memmem])

AC_CONFIG_FILES([Makefile
                 include/Makefile
                 libimg4tool.pc
                 include/img4tool/img4tool.hpp
                 external/libgeneral/include/libgeneral/macros.h
                 external/libgeneral/include/libgeneral/ByteOrder.hpp
                 img4tool/Makefile
                 separttool/Makefile])

AC_OUTPUT


echo "
Configuration for $PACKAGE-$VERSION:
-------------------------------------------

  install prefix ..........: $prefix
  debug build .............: $debug_build
  enable separttool .......: $enable_separttool
  with libfwkeyfetch ......: $with_libfwkeyfetch
  with plist ..............: $with_plist
  with bvx2 ...............: $with_compression
  crypto backend ..........: $crypto_backend"

echo "  compiler ................: ${CC}

  Now type 'make' to build $PACKAGE-$VERSION,
  and then 'make install' for installation.
"
if test "x$crypto_backend" != "xopenssl"; then
  echo "WARNING: crypto_backend is not openssl, some features will be missing"
fi
