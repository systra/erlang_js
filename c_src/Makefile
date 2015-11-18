# This Makefile builds the dependencies (libjs and libnspr) needed by
# spidermonkey_drv.so

SHELL	:= /bin/bash
UNAME	:= $(shell uname -s)
TAR		?= tar
GUNZIP		?= gunzip
NSPR_VER	:= 4.8

ifeq ($(UNAME),SunOS)
	PATCH	?= gpatch
else
	PATCH	?= patch
endif

SYSTEM_DIR := $(CURDIR)/system
LIB_DIR    := $(SYSTEM_DIR)/lib
INC_DIR    := $(SYSTEM_DIR)/include

JS_DIR     := $(CURDIR)/js-1.8.5
NSPR_DIR   := $(CURDIR)/nsprpub
BUILD_DIR  := $(CURDIR)

# NSPR_SIXTYFOUR is defined in erlang_js/rebar.config

js: $(LIB_DIR)/libjs.a

$(LIB_DIR)/libjs.a: $(LIB_DIR)/libnspr4.a
	@rm -rf $(JS_DIR)
	@$(GUNZIP) -c js185-1.0.0.tar.gz | $(TAR) xf -
	# To use system NSPR library remove --with-nspr* options
	# and put --with-system-nspr
	@pushd $(JS_DIR)/js/src; \
		make clean; \
	    ./configure --disable-debug --enable-optimize --enable-threadsafe --with-nspr-cflags="-I$(INC_DIR)/nspr" --with-nspr-libs="-L$(LIB_DIR) -lnspr4" && make JS_DIST=$(SYSTEM_DIR); \
		popd
	@mkdir $(INC_DIR)/js
	@cp $(JS_DIR)/js/src/dist/include/*.h $(INC_DIR)/js
	@cp $(JS_DIR)/js/src/*.tbl $(INC_DIR)/js
	@cp $(JS_DIR)/js/src/dist/lib/libjs_static.a $(LIB_DIR)/libjs.a

$(LIB_DIR)/libnspr4.a:
	$(GUNZIP) -c nsprpub-$(NSPR_VER).tar.gz | $(TAR) xf -
	@for I in patches/nspr-*.patch; do \
		($(PATCH) -p1 < $${I} || echo "Skipping patch"); \
	done
	(cd $(NSPR_DIR) && \
	 ./configure --disable-debug --enable-optimize \
                     --prefix=$(SYSTEM_DIR) $(NSPR_SIXTYFOUR) && \
         $(MAKE) all install)

clean:
	@rm -rf $(SYSTEM_DIR) $(NSPR_DIR) $(JS_DIR)
	@rm -rf *flymake*

nspryoink: deps
	@cd deps ; \
	@cvs -q -d :pserver:anonymous@cvs-mirror.mozilla.org:/cvsroot \
		co -r NSPR_4_8_RTM -d nsprpub mozilla/nsprpub

.EXPORT_ALL_VARIABLES:
