#include ../../../common.mak

LIBEAP_NAME = libeap
LIBEAP_CURRENT = 0
LIBEAP_REVISION = 0
LIBEAP_AGE = 0

LIBEAP = $(LIBEAP_NAME).so.$(LIBEAP_CURRENT).$(LIBEAP_REVISION).$(LIBEAP_AGE)
LIBEAP_SO = $(LIBEAP_NAME).so.$(LIBEAP_CURRENT)

.PHONY: all clean install uninstall

all: $(LIBEAP)
	ln -fs $(LIBEAP) $(LIBEAP_SO)
	ln -fs $(LIBEAP) $(LIBEAP_NAME).so

ifndef CC
CC=gcc
endif

ifndef CFLAGS
CFLAGS = -MMD -O0 -Wall -g
endif

CONFIG_TLS=openssl

INCLUDE_INSTALL_DIR=/usr/include/eap_peer

# Got to use override all across the board, otherwise a 'make
# CFLAGS=XX' will kill us because the command line's CFLAGS will
# overwrite Make's and we'll loose all the infrastructure it sets.
override CFLAGS += -I. -I.. -I../crypto -I../utils -I../common 
ifeq ($(RTCONFIG_BCMARM),y)
override CFLAGS += -lgcc_s
endif

# at least for now, need to include config_ssid.h and config_blob.h from
# wpa_supplicant directory
override CFLAGS += -I ../../wpa_supplicant

SRC_both += ../utils/common.c
SRC_both += ../utils/os_unix.c
SRC_both += ../utils/wpa_debug.c
SRC_both += ../utils/base64.c
SRC_both += ../utils/wpabuf.c
SRC_both += ../crypto/md5.c
SRC_both += ../crypto/rc4.c
SRC_both += ../crypto/md4.c
SRC_both += ../crypto/sha1.c
SRC_both += ../crypto/des.c
SRC_both += ../crypto/aes_wrap.c
SRC_both += ../crypto/aes.c
SRC_both += ../crypto/ms_funcs.c
SRC_both += ../crypto/sha256.c


SRC_both += ../eap_common/eap_peap_common.c
SRC_both += ../eap_common/eap_psk_common.c
SRC_both += ../eap_common/eap_pax_common.c
SRC_both += ../eap_common/eap_sake_common.c
SRC_both += ../eap_common/eap_gpsk_common.c
SRC_both += ../eap_common/chap.c

SRC_peer += ../eap_peer/eap_tls.c
SRC_peer += ../eap_peer/eap_peap.c
SRC_peer += ../eap_peer/eap_ttls.c
SRC_peer += ../eap_peer/eap_md5.c
SRC_peer += ../eap_peer/eap_mschapv2.c
SRC_peer += ../eap_peer/mschapv2.c
SRC_peer += ../eap_peer/eap_otp.c
SRC_peer += ../eap_peer/eap_gtc.c
SRC_peer += ../eap_peer/eap_leap.c
SRC_peer += ../eap_peer/eap_psk.c
SRC_peer += ../eap_peer/eap_pax.c
SRC_peer += ../eap_peer/eap_sake.c
SRC_peer += ../eap_peer/eap_gpsk.c
SRC_peer += ../eap_peer/eap.c
SRC_peer += ../eap_common/eap_common.c
SRC_peer += ../eap_peer/eap_methods.c
SRC_peer += ../eap_peer/eap_tls_common.c

override CFLAGS += -DEAP_TLS
override CFLAGS += -DEAP_PEAP
override CFLAGS += -DEAP_TTLS
override CFLAGS += -DEAP_MD5
override CFLAGS += -DEAP_MSCHAPv2
override CFLAGS += -DEAP_GTC
override CFLAGS += -DEAP_OTP
override CFLAGS += -DEAP_LEAP
override CFLAGS += -DEAP_PSK
override CFLAGS += -DEAP_PAX
override CFLAGS += -DEAP_SAKE
override CFLAGS += -DEAP_GPSK -DEAP_GPSK_SHA256
override CFLAGS += -DEAP_TLS_FUNCS

override CFLAGS += -DIEEE8021X_EAPOL

ifeq ($(CONFIG_TLS), openssl)
override CFLAGS += -DEAP_TLS_OPENSSL
override CFLAGS += -I$(TOP)/openssl-1.0.0q/include
SRC_both += ../crypto/tls_openssl.c
SRC_both += ../crypto/crypto_openssl.c
LIBS += -L$(TARGET_DIR)/usr/lib -lssl -lcrypto
override CFLAGS += -DINTERNAL_SHA256
endif

ifeq ($(CONFIG_TLS), internal)
SRC_both += ../crypto/tls_internal.c
SRC_both += ../tls/tlsv1_common.c ../../tls/tlsv1_record.c
SRC_both += ../tls/tlsv1_cred.c
SRC_both += ../tls/asn1.c ../../tls/x509v3.c
SRC_both += ../crypto/crypto_internal.c ../../tls/rsa.c ../../tls/bignum.c

SRC_peer += ../tls/tlsv1_client.c
SRC_peer += ../tls/tlsv1_client_write.c ../../tls/tlsv1_client_read.c
override CFLAGS += -DCONFIG_TLS_INTERNAL_CLIENT

SRC_server += ../tls/tlsv1_server.c
SRC_server += ../tls/tlsv1_server_write.c ../../tls/tlsv1_server_read.c
override CFLAGS += -DCONFIG_TLS_INTERNAL_SERVER

override CFLAGS += -DCONFIG_TLS_INTERNAL
override CFLAGS += -DCONFIG_CRYPTO_INTERNAL
override CFLAGS += -DCONFIG_INTERNAL_X509
override CFLAGS += -DINTERNAL_AES
override CFLAGS += -DINTERNAL_SHA1
override CFLAGS += -DINTERNAL_SHA256
override CFLAGS += -DINTERNAL_MD5
override CFLAGS += -DINTERNAL_MD4
override CFLAGS += -DINTERNAL_DES
ifdef CONFIG_INTERNAL_LIBTOMMATH
override CFLAGS += -DCONFIG_INTERNAL_LIBTOMMATH
else
LIBS += -ltommath
endif
endif

ifndef LDO
LDO=$(CC)
endif


OBJS_lib=$(addsuffix -eap.o,$(basename $(SRC_both) $(SRC_peer)))

 #$(SRC_server)

override CFLAGS  += -fPIC -DPIC
LDFLAGS += -shared
ifeq ($(RTCONFIG_BCMARM),y)
LDFLAGS += -lgcc_s
endif

$(LIBEAP): $(OBJS_lib)
	$(LDO) $(LDFLAGS) $(OBJS_lib) -Wl,-soname -Wl,$(LIBEAP_SO) -o $(LIBEAP) $(LIBS)


%-eap.o: %.c
	$(CC) -c -o $@ $(CFLAGS) $<


UTIL_HEADERS = ../utils/includes.h ../utils/common.h \
	../utils/wpabuf.h ../utils/build_config.h \
	../utils/os.h ../utils/wpa_debug.h
COMMON_HEADERS = ../common/defs.h 
EAP_COMMON_HEADERS = ../eap_common/eap_defs.h 
MAIN_HEADERS = eap.h eap_methods.h eap_config.h
CRYPTO_HEADERS =  ../crypto/tls.h  

install: 

	mkdir -p $(DESTDIR)/usr/lib
#	copy the lib file to std lib location
	cp $(LIBEAP) $(DESTDIR)/usr/lib
	ln -fs $(LIBEAP_SO) $(DESTDIR)/usr/lib/$(LIBEAP_NAME).so

#	copy the headers reqd by apps using eap peer library in its own subfolder under /usr/include
	mkdir -p \
		$(DESTDIR)/$(INCLUDE_INSTALL_DIR)/eap_common \
		$(DESTDIR)/$(INCLUDE_INSTALL_DIR)/common \
		$(DESTDIR)/$(INCLUDE_INSTALL_DIR)/util \
		$(DESTDIR)/$(INCLUDE_INSTALL_DIR)/crypto
	install -m 0644 $(EAP_COMMON_HEADERS) $(DESTDIR)/$(INCLUDE_INSTALL_DIR)/eap_common
	install -m 0644 $(COMMON_HEADERS) $(DESTDIR)/$(INCLUDE_INSTALL_DIR)/common
	install -m 0644 $(CRYPTO_HEADERS) $(DESTDIR)/$(INCLUDE_INSTALL_DIR)/crypto
	install -m 0644 $(UTIL_HEADERS) $(DESTDIR)/$(INCLUDE_INSTALL_DIR)/util
	install -m 0644 $(MAIN_HEADERS) $(DESTDIR)/$(INCLUDE_INSTALL_DIR)/

	mkdir -p $(DESTDIR)/usr/lib/pkgconfig
	cp libeap0.pc $(DESTDIR)/usr/lib/pkgconfig

uninstall: 

	rm $(DESTDIR)/usr/lib/$(LIBEAP)
	rm -fr $(DESTDIR)/$(INCLUDE_INSTALL_DIR)
	rm -f $(DESTDIR)/usr/lib/pkgconfig/libeap0.pc

clean:
	rm -f *~ *.o *.so *.d libeap.* $(LIBEAP) $(OBJS_lib)

-include $(OBJS:%.o=%.d)
