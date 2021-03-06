#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#

#
# Copyright (c) 2015, 2018, Oracle and/or its affiliates. All rights reserved.
#

# parfait is a bit unhappy (22527218)
export PARFAIT_BUILD=no

BUILD_BITS = 32
COMPILER = gcc

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		thunderbird
COMPONENT_VERSION=	52.7.0
COMPONENT_PROJECT_URL=	http://www.mozilla.com/thunderbird
COMPONENT_ARCHIVE = 	$(COMPONENT_SRC).source.tar.xz
COMPONENT_ARCHIVE_HASH= \
    sha256:82462890d393ed78c03226924a6166596b7b6d1562b5071e713a7558cd292ea3
ifndef CANDIDATE_BUILD
MOZILLA_FTP = 		http://ftp.mozilla.org/pub/$(COMPONENT_NAME)/releases/$(COMPONENT_VERSION)
else
MOZILLA_FTP = 		http://ftp.mozilla.org/pub/$(COMPONENT_NAME)/candidates/$(COMPONENT_VERSION)-candidates/build$(CANDIDATE_BUILD)
endif
COMPONENT_ARCHIVE_URL=	$(MOZILLA_FTP)/source/$(COMPONENT_ARCHIVE)
COMPONENT_BUGDB=	tbird/mailer
COMPONENT_ANITYA_ID=	4967

TPNO=			41900

LIGHTNING_VERSION=	5.4.7

# There are a set of .xpi files (as given in the LANG_LIST definition
# below) that we need. We could download and unpack them individually, but
# they doesn't contain version in its file name and there is lot of them.
# Therefore we will download them and archive them in Userland cache using
# one file with version number.
COMPONENT_ARCHIVE_1 =   	$(COMPONENT_SRC)-xpi.tar.bz2
COMPONENT_ARCHIVE_HASH_1 = \
    sha256:f13f8ce252fd534fdab803e7449440087c738e150719a9b31dfba0c2d34a920b
COMPONENT_ARCHIVE_URL_1 =       make://download-xpis/$(COMPONENT_ARCHIVE_1)

COMPONENT_ARCHIVE_2 =		lightning-$(LIGHTNING_VERSION)-xpi.tar.bz2
COMPONENT_ARCHIVE_HASH_2 = \
    sha256:5ec1471850d30b3d46f01b38f09a60126a6be1f0b17b042f98bc95a19bb6797d
COMPONENT_ARCHIVE_URL_2 = 	make://download-lightning-xpis/$(COMPONENT_ARCHIVE_2)

LANG_LIST = ar bg ca cs da de el es-AR es-ES et eu fi \
	fr gl he hu id is it ja ko lt nb-NO nl nn-NO pa-IN pl \
	pt-BR pt-PT ro ru sk sl sq sv-SE tr uk zh-CN zh-TW

CLEAN_PATHS += $(COMPONENT_DIR)/xpi
CLEAN_PATHS += $(COMPONENT_DIR)/xpi-lightning 

# Firefox and Thunderbird share the same firefox*.patch patches. Copy these 
# patches from the Firefox patches directory and the Thunderbird patches to a 
# directory of all files (patches-all), except for Firefox patches 42,
# which don't get applied in Thunderbird. (dtrace patch)
PATCH_DIR=patches-all
CLEAN_PATHS += $(PATCH_DIR)
COPY_COMMON_FILES_1:= $(shell mkdir -p $(PATCH_DIR))
COPY_COMMON_FILES_2:=  \
    $(shell /usr/bin/rsync -ac patches/* ../firefox/patches/* $(PATCH_DIR))
COPY_COMMON_FILES_3:= \
    $(shell $(RM) $(PATCH_DIR)/firefox*42*.patch)

include $(WS_MAKE_RULES)/common.mk

# /usr/sbin for dtrace, SPRO for python modules, PERL as well.
PATH = $(dirname $(CC)):/usr/gnu/bin:/usr/bin:/usr/sbin:$(dirname $(PERL)):$(SPRO_VROOT)/bin

MOZCONFIG =	$(BUILD_DIR_32)/mozconfig

COMPONENT_PRE_CONFIGURE_ACTION += \
	echo "mk_add_options BUILD_OFFICIAL=1" > $(MOZCONFIG) ; \
	echo "mk_add_options MOZILLA_OFFICIAL=1" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-application=mail" >> $(MOZCONFIG) ; \
	echo "ac_add_options --prefix=/usr" >> $(MOZCONFIG) ; \
	echo "ac_add_options --libdir=/usr/lib" >> $(MOZCONFIG) ; \
	echo "ac_add_options --disable-debug" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-optimize" >> $(MOZCONFIG) ; \
	echo "ac_add_options --disable-tests" >> $(MOZCONFIG) ; \
	echo "ac_add_options --with-system-libevent" >> $(MOZCONFIG) ; \
	echo "ac_add_options --with-system-zlib" >> $(MOZCONFIG) ; \
	echo "ac_add_options --with-system-bz2" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-system-ffi" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-system-sqlite" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-jemalloc" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-official-branding" >> $(MOZCONFIG) ; \
	echo "ac_add_options --disable-updater" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-system-pixman" >> $(MOZCONFIG) ; \
	echo "ac_add_options --with-intl-api" >> $(MOZCONFIG) ; \
	echo "ac_add_options --disable-crashreporter" >> $(MOZCONFIG) ; \
	echo "ac_add_options --disable-debug-symbols" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-calendar" >> $(MOZCONFIG) ; \
	echo "ac_add_options --enable-startup-notification" >> $(MOZCONFIG) ; \
        echo "ac_add_options --enable-js-shell" >> $(MOZCONFIG) ; \
        echo "ac_add_options --enable-default-toolkit=cairo-gtk2" >> $(MOZCONFIG) ;

CXXFLAGS += 		-Wno-invalid-offsetof -fpermissive
CC +=		$(CC_BITS)
CXX +=		$(CC_BITS)

# jemalloc (from libxul) interposes on malloc in glib causing startup problems,
# so use the big hammer for now, pending the resolution of:
#    24765609 thunderbird build dies with newer GCC 
LD_EXEC_OPTIONS += -z now

CONFIGURE_ENV +=	PKG_CONFIG_PATH=$(CONFIGURE_LIBDIR.$(BITS))/pkgconfig
CONFIGURE_ENV +=	OS_DEFINES="-D__USE_LEGACY_PROTOTYPES__"
# -L.../dist/bin needs to be set otherwise it will report missing libnss3.so, 
# libssl3.so, etc, while linking.
CONFIGURE_ENV +=	LDFLAGS="$(LD_Z_IGNORE) -z interpose -B nodirect -L$(BUILD_DIR_32)/dist/lib -L$(BUILD_DIR_32)/dist/bin"

CONFIGURE_ENV +=	MOZCONFIG=$(MOZCONFIG)
CONFIGURE_ENV +=	"CC=$(CC)"
CONFIGURE_ENV +=	"CXX=$(CXX)"
CONFIGURE_ENV += 	"CFLAGS=$(CFLAGS)"
CONFIGURE_ENV +=	"CXXFLAGS=$(CXXFLAGS)"

# If the configure options are not explicitly set, then configure fails with:
# configure: warning: CC=/usr/gcc/4.8/bin/gcc: invalid host type
# invalid host type
# configure: warning: CXX=/usr/gcc/4.8/bin/gcc: invalid host type
# invalid host type
# configure: error: can only configure for one host and one target at a time
CONFIGURE_OPTIONS = --prefix=$(CONFIGURE_PREFIX)

#because we touched old-configure.in and js/src/old-configure.in
COMPONENT_PRE_CONFIGURE_ACTION += (cd $(COMPONENT_NAME)-$(COMPONENT_VERSION)/mozilla; autoreconf; touch configure; cd js/src; autoreconf; touch configure);

COMPONENT_POST_CONFIGURE_ACTION += \
        $(CP) $(MOZCONFIG) $(COMPONENT_SRC)/mozconfig

COMPONENT_POST_BUILD_ACTION = \
        (cd $(@D)/mail/installer ; $(ENV) $(COMPONENT_BUILD_ENV) \
                $(GMAKE) $(COMPONENT_BUILD_ARGS) $(COMPONENT_BUILD_TARGETS))

# Path to lightning extension directory
LIGHTNING_EXT_DIR = \
	$(PROTOUSRLIBDIR)/thunderbird-$(COMPONENT_VERSION)/distribution/extensions/{e2fda1a4-762b-4020-b5ad-a41df1933103}

COMPONENT_POST_INSTALL_ACTION += \
	$(CP) -r $(BUILD_DIR_32)/dist/bin/distribution/extensions/{e2fda1a4-762b-4020-b5ad-a41df1933103}/* \
		$(LIGHTNING_EXT_DIR) ;

COMPONENT_POST_INSTALL_ACTION += \
        $(RM) -rf xpi ; \
        $(TAR) xvf $(COMPONENT_ARCHIVE_1) > /dev/null 2>&1 ; \
	$(TAR) xvf $(COMPONENT_ARCHIVE_2) > /dev/null 2>&1 ; \
        list1='$(LANG_LIST)' ; for f in $$list1 ; do \
            $(CP) xpi/$$f.xpi \
                $(PROTOUSRLIBDIR)/thunderbird-$(COMPONENT_VERSION)/distribution/extensions/langpack-$$f@thunderbird.mozilla.org.xpi ; \
	    unzip xpi-lightning/lightning-$(LIGHTNING_VERSION).$$f.linux-i686.xpi \
		chrome/calendar-$$f/\* chrome/lightning-$$f/\* -d \
		    $(LIGHTNING_EXT_DIR)/ ; \
	    echo 'locale calendar '$$f' chrome/calendar-'$$f'/locale/'$$f'/calendar/' >> $(LIGHTNING_EXT_DIR)/chrome.manifest ; \
	    echo 'locale lightning '$$f' chrome/lightning-'$$f'/locale/'$$f'/lightning/' >> $(LIGHTNING_EXT_DIR)/chrome.manifest ; \
	done ;

TB_XPI = $(MOZILLA_FTP)/linux-i686/xpi
LN_XPI = http://ftp.mozilla.org/pub/calendar/lightning/candidates/$(LIGHTNING_VERSION)-candidates/build1/linux-i686

# Run gmake download-xpis and gmake download-lightning-xpis to download tarballs of xpi
# for thunderbird and lightning separately.
download-xpis:
	@echo "download-xpis"
	$(RM) -r $(COMPONENT_DIR)/xpi $(COMPONENT_DIR)/$(COMPONENT_ARCHIVE_1) 
	$(MKDIR) $(COMPONENT_DIR)/xpi
	@echo "downloading thunderbird xpis"
	list1='$(LANG_LIST)' ; for f in $$list1 ; do \
		echo $$f; \
		/usr/bin/curl -s -o $(COMPONENT_DIR)/xpi/$$f.xpi \
			$(TB_XPI)/$$f.xpi ;\
		file $(COMPONENT_DIR)/xpi/$$f.xpi | grep ZIP > /dev/null; \
		if [ $$? -ne 0 ]; then \
			echo "File $$f is not ZIP archive!"; exit 1; \
		fi ; \
	done
	(cd $(COMPONENT_DIR); \
	    TZ=UTC gtar cjvf $(USERLAND_ARCHIVES)$(COMPONENT_ARCHIVE_1) --mtime='1970-01-01' --owner=root --group=root xpi/* )
	/usr/bin/sha256sum $(USERLAND_ARCHIVES)$(COMPONENT_ARCHIVE_1)

download-lightning-xpis:
	@echo "download-lightning-xpis"
	$(RM) -r $(COMPONENT_DIR)/xpi-lightning $(COMPONENT_DIR)/$(COMPONENT_ARCHIVE_2)
	$(MKDIR) $(COMPONENT_DIR)/xpi-lightning
	@echo "downloading lightning xpis"
	list1='$(LANG_LIST)' ; for f in $$list1 ; do \
		echo $$f; \
		/usr/bin/curl -s -o $(COMPONENT_DIR)/xpi-lightning/lightning-$(LIGHTNING_VERSION).$$f.linux-i686.xpi \
	                $(LN_XPI)/lightning-$(LIGHTNING_VERSION).$$f.linux-i686.xpi ;\
		file $(COMPONENT_DIR)/xpi-lightning/lightning-$(LIGHTNING_VERSION).$$f.linux-i686.xpi | grep ZIP > /dev/null; \
		if [ $$? -ne 0 ]; then \
			echo "File $$f is not ZIP archive!"; exit 1; \
		fi ; \
	done
	(cd $(COMPONENT_DIR); \
	    TZ=UTC gtar cjvf $(USERLAND_ARCHIVES)$(COMPONENT_ARCHIVE_2) --mtime='1970-01-01' --owner=root --group=root xpi-lightning/* )
	/usr/bin/sha256sum $(USERLAND_ARCHIVES)$(COMPONENT_ARCHIVE_2)

COMPONENT_TEST_DIR =    $(@D)/js/src
COMPONENT_TEST_TARGETS=check-jit-test
COMPONENT_TEST_TRANSFORMS = \
	'-e "/^PASSED ALL/,/Failed:/p" ' \
	'-e "/.*/d" '

$(SOURCE_DIR)/.patched-firefox-%: GPATCH_FLAGS += -d $(@D)/mozilla

REQUIRED_PACKAGES += compress/bzip2
REQUIRED_PACKAGES += database/sqlite-3
REQUIRED_PACKAGES += developer/build/autoconf-213
ifneq ($(MACH), sparc)
REQUIRED_PACKAGES += developer/yasm
endif
REQUIRED_PACKAGES += library/desktop/atk
REQUIRED_PACKAGES += library/desktop/cairo
REQUIRED_PACKAGES += library/desktop/gdk-pixbuf
REQUIRED_PACKAGES += library/desktop/gtk2
REQUIRED_PACKAGES += library/desktop/pango
REQUIRED_PACKAGES += library/desktop/startup-notification
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/graphics/pixman
REQUIRED_PACKAGES += library/libevent
REQUIRED_PACKAGES += library/libffi
REQUIRED_PACKAGES += library/samba/libsmbclient
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/gcc/gcc-c-runtime
REQUIRED_PACKAGES += system/library/gcc/gcc-c++-runtime
REQUIRED_PACKAGES += system/library/fontconfig
REQUIRED_PACKAGES += system/library/freetype-2
REQUIRED_PACKAGES += system/library/libdbus
REQUIRED_PACKAGES += system/library/libdbus-glib
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxcb
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxrender
REQUIRED_PACKAGES += x11/library/toolkit/libxt
