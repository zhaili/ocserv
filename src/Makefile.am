SUBDIRS =

AM_CPPFLAGS = -I$(srcdir)/../gl/ -I$(builddir)/../gl/ \
	-I$(srcdir)/ -I$(builddir)/../ $(LIBOPTS_CFLAGS) \
	$(LIBGNUTLS_CFLAGS) \
	$(LIBPROTOBUF_C_CFLAGS) $(LIBLZ4_CFLAGS) \
	$(LIBNL3_CFLAGS) $(LIBREADLINE_CFLAGS) \
	$(LIBTALLOC_CFLAGS) $(LIBDBUS_CFLAGS) \
	$(LIBKRB5_CFLAGS) $(LIBTASN1_CFLAGS) $(RADCLI_CFLAGS)

BUILT_SOURCES = ocpasswd-args.c ocpasswd-args.h \
	ocserv-args.c ocserv-args.h ipc.pb-c.c ipc.pb-c.h \
	ctl.pb-c.c ctl.pb-c.h http-heads.h kkdcp_asn1_tab.c

#AM_CPPFLAGS += -DDEBUG_LEAKS

if LOCAL_HTTP_PARSER
AM_CPPFLAGS += -I$(srcdir)/http-parser/
HTTP_PARSER_SOURCES = http-parser/http_parser.c http-parser/http_parser.h
NEEDED_HTTP_PARSER_LIBS = 
else
NEEDED_HTTP_PARSER_LIBS = $(HTTP_PARSER_LIBS)
endif

if NEED_LIBOPTS
NEEDED_LIBOPTS = ../libopts/libopts.a
else
NEEDED_LIBOPTS = $(LIBOPTS_LDADD)
endif

EXTRA_DIST = ccan/licenses/BSD-MIT version.inc.in \
	ccan/licenses/CC0 ccan/licenses/LGPL-2.1 version.inc \
	occtl-args.def ipc.proto ctl.proto http-heads.gperf

bin_PROGRAMS = ocpasswd occtl

sbin_PROGRAMS = ocserv

CCAN_SOURCES = ccan/build_assert/build_assert.h ccan/check_type/check_type.h \
	ccan/container_of/container_of.h ccan/hash/hash.c ccan/hash/hash.h \
	ccan/htable/htable.c ccan/htable/htable.h ccan/htable/htable_type.h \
	ccan/list/list.c ccan/list/list.h

if LOCAL_TALLOC
CCAN_SOURCES += ccan/talloc/talloc.c ccan/talloc/talloc.h ccan/compiler/compiler.h \
	ccan/typesafe_cb/typesafe_cb.h
endif


noinst_LIBRARIES = libcmd-ocserv.a
libcmd_ocserv_a_SOURCES = ocserv-args.def ocserv-args.c ocserv-args.h

ocserv-args.c: $(srcdir)/ocserv-args.def $(builddir)/version.inc
	$(AUTOGEN) $<
ocserv-args.h: ocserv-args.c

PROTOBUF_SOURCES =
if LOCAL_PROTOBUF_C
PROTOBUF_SOURCES += protobuf/protobuf-c/protobuf-c.h protobuf/protobuf-c/protobuf-c.c
endif

# Files common to ocserv and occtl.
COMMON_SOURCES=common.c common.h system.c system.h setproctitle.c setproctitle.h

# Authentication module sources
AUTH_SOURCES=auth/pam.c auth/pam.h auth/plain.c auth/plain.h auth/radius.c auth/radius.h \
	auth/common.c auth/common.h auth/gssapi.h auth/gssapi.c auth-unix.c \
	auth-unix.h

ACCT_SOURCES=acct/pam.c acct/pam.h acct/radius.c acct/radius.h

ocserv_SOURCES = main.c main-auth.c worker-vpn.c worker-auth.c tlslib.c \
	cookies.c main-misc.c ip-lease.c ip-lease.h \
	vpn.h cookies.h tlslib.h log.c tun.c tun.h config-kkdcp.c \
	config.c worker-resume.c worker.h main-resume.c main.h \
	worker-extras.c html.c html.h worker-http.c \
	main-user.c worker-misc.c route-add.c route-add.h worker-privs.c \
	sec-mod.c sec-mod-db.c sec-mod-auth.c sec-mod-auth.h sec-mod.h \
	script-list.h $(COMMON_SOURCES) $(AUTH_SOURCES) $(ACCT_SOURCES) \
	icmp-ping.c icmp-ping.h worker-kkdcp.c subconfig.c \
	sec-mod-sup-config.c sec-mod-sup-config.h \
	sup-config/file.c sup-config/file.h main-sec-mod-cmd.c \
	sup-config/radius.c sup-config/radius.h \
	worker-bandwidth.c worker-bandwidth.h ctl.h main-ctl.h \
	vasprintf.c vasprintf.h \
	proc-search.c proc-search.h http-heads.h \
	main-ban.c main-ban.h common-config.h \
	str.c str.h gettime.h $(CCAN_SOURCES) $(HTTP_PARSER_SOURCES) \
	$(PROTOBUF_SOURCES) sec-mod-acct.h

if ENABLE_COMPRESSION
ocserv_SOURCES += lzs.c lzs.h
endif

if HAVE_GSSAPI
ocserv_SOURCES += kkdcp_asn1_tab.c kkdcp.asn 
endif

ocserv_SOURCES += ipc.pb-c.h ipc.pb-c.c ctl.pb-c.c ctl.pb-c.h

ocserv_LDADD = ../gl/libgnu.a $(NEEDED_LIBOPTS) libcmd-ocserv.a
ocserv_LDADD += $(LIBGNUTLS_LIBS) $(PAM_LIBS) $(LIBUTIL) \
	$(LIBSECCOMP) $(LIBWRAP) $(LIBCRYPT) $(NEEDED_HTTP_PARSER_LIBS) \
	$(LIBPROTOBUF_C_LIBS) $(LIBSYSTEMD) $(LIBTALLOC_LIBS) \
	$(RADCLI_LIBS) $(LIBLZ4_LIBS) $(LIBKRB5_LIBS) \
	$(LIBTASN1_LIBS)


if PCL
ocserv_LDADD += $(PCL_LIBS)
else
ocserv_LDADD += pcl/libpcl.a
SUBDIRS += pcl
AM_CPPFLAGS += -I$(srcdir)/pcl/
endif

noinst_LIBRARIES += libcmd-ocpasswd.a
libcmd_ocpasswd_a_SOURCES = ocpasswd-args.def ocpasswd-args.c ocpasswd-args.h


ocpasswd_SOURCES = ocpasswd.c
ocpasswd_LDADD = ../gl/libgnu.a $(NEEDED_LIBOPTS) libcmd-ocpasswd.a
ocpasswd_LDADD += $(LIBGNUTLS_LIBS) $(LIBCRYPT)

ocpasswd-args.c: $(srcdir)/ocpasswd-args.def $(builddir)/version.inc
	$(AUTOGEN) $<
ocpasswd-args.h: ocpasswd-args.c

occtl_SOURCES = occtl.c occtl-pager.c occtl.h occtl-time.c occtl-cache.c occtl-ip-cache.c \
	occtl-nl.c ctl.h ctl.pb-c.c ctl.pb-c.h $(CCAN_SOURCES) $(COMMON_SOURCES) \
	occtl-print.c common-json.c common-json.h $(PROTOBUF_SOURCES)
occtl_LDADD = ../gl/libgnu.a $(LIBREADLINE_LIBS) \
	$(LIBNL3_LIBS) $(LIBPROTOBUF_C_LIBS) $(LIBTALLOC_LIBS)

ocserv_SOURCES += main-ctl-unix.c
occtl_SOURCES += occtl-unix.c

ipc.pb-c.c: ipc.proto
	protoc-c --c_out=. --proto_path=$(srcdir) $<

ipc.pb-c.h: ipc.pb-c.c

ctl.pb-c.c: ctl.proto
	protoc-c --c_out=. --proto_path=$(srcdir) $<

ctl.pb-c.h: ctl.pb-c.c

http-heads.h: $(srcdir)/http-heads.gperf
	-gperf --global-table -t $^ > $@-tmp && mv $@-tmp $@

kkdcp_asn1_tab.c: $(srcdir)/kkdcp.asn
	-asn1Parser $^ $@
