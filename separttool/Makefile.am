AM_CFLAGS = -I$(top_srcdir)/include $(GLOBAL_CFLAGS) $(libplist_CFLAGS) $(openssl_CFLAGS) $(libcurl_CFLAGS) $(zlib_CFLAGS) -I$(top_srcdir)/external/libfragmentzip/include -I$(top_srcdir)/external/libgeneral/include -I$(top_builddir)/external/libgeneral/include -I$(top_srcdir)/external/jssy/include -I$(top_srcdir)/external/libfwkeyfetch/include
AM_CXXFLAGS = $(AM_CFLAGS) $(GLOBAL_CXXFLAGS)
AM_LDFLAGS = $(libplist_LIBS) $(openssl_LIBS) $(libcurl_LIBS) $(zlib_LIBS)

if ENABLE_SEPARTTOOL

bin_PROGRAMS = separttool

separttool_CFLAGS = $(AM_CFLAGS)
separttool_CXXFLAGS = $(AM_CXXFLAGS)
separttool_LDFLAGS = $(AM_LDFLAGS)
# Link against static libimg4tool.a (single self-contained binary,
# no libimg4tool.so dependency / no libtool wrapper).
separttool_LDADD = ../img4tool/.libs/libimg4tool.a ../img4tool/libgeneral.a ../img4tool/libfwkeyfetch.a ../img4tool/libfragmentzip.a ../img4tool/libjssy.a
separttool_DEPENDENCIES = ../img4tool/libimg4tool.la
separttool_SOURCES = 	main.cpp

endif #ENABLE_SEPARTTOOL
