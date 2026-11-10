AM_CFLAGS = -I$(top_srcdir)/include $(GLOBAL_CFLAGS) $(libplist_CFLAGS) $(openssl_CFLAGS) $(libcurl_CFLAGS) $(zlib_CFLAGS) -I$(top_srcdir)/external/libfragmentzip/include -I$(top_srcdir)/external/libgeneral/include -I$(top_builddir)/external/libgeneral/include -I$(top_srcdir)/external/jssy/include -I$(top_srcdir)/external/libfwkeyfetch/include
AM_CXXFLAGS = $(AM_CFLAGS) $(GLOBAL_CXXFLAGS)
AM_LDFLAGS = $(libplist_LIBS) $(openssl_LIBS) $(libcurl_LIBS) $(zlib_LIBS)

noinst_LIBRARIES = libjssy.a libgeneral.a libfragmentzip.a libfwkeyfetch.a

# -fPIC is required so these archives can be linked into shared
# libimg4tool.so when building with shared libs enabled (default).
libjssy_a_CFLAGS = $(AM_CFLAGS) -fPIC
libjssy_a_SOURCES = $(top_srcdir)/external/jssy/jssy/jssy.c $(top_srcdir)/external/jssy/jssy/jssy_create.c

# Vendored libgeneral (full build: img4tool needs Mem/Utils/ByteOrder).
libgeneral_a_CXXFLAGS = $(AM_CFLAGS) -fPIC
libgeneral_a_SOURCES = $(top_srcdir)/external/libgeneral/libgeneral/exception.cpp $(top_srcdir)/external/libgeneral/libgeneral/Event.cpp $(top_srcdir)/external/libgeneral/libgeneral/TaggedEvent.cpp $(top_srcdir)/external/libgeneral/libgeneral/GuardAccess.cpp $(top_srcdir)/external/libgeneral/libgeneral/Manager.cpp $(top_srcdir)/external/libgeneral/libgeneral/Mem.cpp $(top_srcdir)/external/libgeneral/libgeneral/Utils.cpp

# Vendored libfragmentzip (single C source, statically linked).
libfragmentzip_a_CFLAGS = $(AM_CFLAGS) -fPIC
libfragmentzip_a_SOURCES = $(top_srcdir)/external/libfragmentzip/libfragmentzip/libfragmentzip.c

# Vendored libfwkeyfetch (Kaiden-AC fork, single C++ source, statically linked).
libfwkeyfetch_a_CXXFLAGS = $(AM_CFLAGS) -fPIC
libfwkeyfetch_a_SOURCES = $(top_srcdir)/external/libfwkeyfetch/libfwkeyfetch/libfwkeyfetch.cpp

lib_LTLIBRARIES = libimg4tool.la
bin_PROGRAMS = img4tool

libimg4tool_la_CFLAGS = $(AM_CFLAGS)
libimg4tool_la_CXXFLAGS = $(AM_CXXFLAGS)
libimg4tool_la_LDFLAGS = $(AM_LDFLAGS)
libimg4tool_la_LIBADD = libgeneral.a
libimg4tool_la_SOURCES = 	ASN1DERElement.cpp \
                            lzssdec.cpp \
                            img4tool.cpp \
                            separttool.cpp

img4tool_CFLAGS = $(AM_CFLAGS)
img4tool_CXXFLAGS = $(AM_CXXFLAGS)
img4tool_LDFLAGS = $(AM_LDFLAGS)
# Link executables against the static libimg4tool.a (not libimg4tool.la) so
# the binaries are self-contained single files with no libimg4tool.so
# dependency and no libtool wrapper script.
img4tool_LDADD = .libs/libimg4tool.a libgeneral.a libfwkeyfetch.a libfragmentzip.a libjssy.a
# Build the libtool library (and thus .libs/libimg4tool.a) before linking.
img4tool_DEPENDENCIES = libimg4tool.la

img4tool_SOURCES = 	main.cpp
