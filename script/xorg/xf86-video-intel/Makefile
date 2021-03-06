GARNAME = xf86-video-intel
GARVERSION_TYPE = git
GARVERSION = $(strip $(if $(filter git,$(GARVERSION_TYPE)) , \
	20170820-2100efa, \
	2.99.17 ))
CATEGORIES = $(CATEGORY)
MASTER_SITES = $(strip $(if $(filter git,$(GARVERSION_TYPE)) , \
	git://anongit.freedesktop.org/git/xorg/driver/xf86-video-intel/, \
	$(XORG_MASTER_SITES) ))
DISTFILES = $(DISTNAME).tar.bz2
PATCHFILES = $(GARNAME)-pkg_config.patch

LICENSE = $(GARNAME)
$(GARNAME)_LICENSE_TEXT = $(WORKSRC)/COPYING

DESCRIPTION = 
define BLURB
endef

DEPENDS = lang/c system/eudev \
	$(CATEGORY)/xorgproto \
	$(CATEGORY)/libdrm \
	$(CATEGORY)/libpciaccess \
	$(CATEGORY)/libX11 \
	$(CATEGORY)/libXext \
	$(CATEGORY)/libXfixes \
	$(CATEGORY)/libXrender \
	$(CATEGORY)/libXvMC \
	$(CATEGORY)/pixman \
	$(CATEGORY)/util-macros \
	$(CATEGORY)/xcb-util \
	$(CATEGORY)/xorg-server

CATEGORY := $(shell basename $(shell dirname $(shell pwd)))

CONFIGURE_SCRIPTS = $(WORKSRC)/configure
BUILD_SCRIPTS     = $(WORKSRC)/Makefile
INSTALL_SCRIPTS   = $(WORKSRC)/Makefile

CONFIGURE_ARGS = $(DIRPATHS) --build=$(GARBUILD) --host=$(GARHOST) \
	--cache-file=config.cache \
	--disable-silent-rules \
	--disable-static \
	--enable-shared \
	--enable-udev \
	--enable-dri \
	--disable-xvmc \
	--disable-kms-only \
	--disable-ums-only \
	--enable-sna \
	--enable-uxa \
	--disable-xaa \
	--disable-dga \
	--disable-async-swap \
	--disable-debug \
	--with-gnu-ld

CONFIGURE_ENV = \
	XORG_SGML_PATH="$(DESTDIR)$(includedir)/xorg"

GAR_EXTRA_CONF += $(CATEGORY)/xorg/package-api.mk
include ../../gar.mk

# drier fails to compile with gcc 5.3.0's link time optimization enabled.
CFLAGS   := $(filter-out -flto, $(CFLAGS))
CXXFLAGS := $(filter-out -flto, $(CXXFLAGS))
LDFLAGS  := $(filter-out -flto, $(LDFLAGS))

git//%$(DISTNAME).tar.bz2:
	@$(call FETCH_GIT, $*, $(word 2,$(subst -, ,$(GARVERSION))), $(DISTNAME))
	@$(MAKECOOKIE)

pre-configure:
	@$(call RUN_AUTOTOOLS)
	@$(call FIX_LIBTOOL,$(WORKSRC)/configure)
	@rm -f $(WORKSRC)/config.cache
	@echo "`echo ac_cv_file_$(DESTDIR)$(includedir)/xorg/dri.h | sed -e 's%[^a-zA-Z0-9]%_%g'`=yes"       >> $(WORKSRC)/config.cache
	@echo "`echo ac_cv_file_$(DESTDIR)$(includedir)/xorg/sarea.h | sed -e 's%[^a-zA-Z0-9]%_%g'`=yes"     >> $(WORKSRC)/config.cache
	@echo "`echo ac_cv_file_$(DESTDIR)$(includedir)/xorg/dristruct.h | sed -e 's%[^a-zA-Z0-9]%_%g'`=yes" >> $(WORKSRC)/config.cache
	@$(MAKECOOKIE)

post-install:
	@rm -f $(DESTDIR)$(libdir)/libI810XvMC.la
	@rm -f $(DESTDIR)$(libdir)/libIntelXvMC.la
	@rm -f $(DESTDIR)$(libdir)/xorg/modules/drivers/intel_drv.la
	@cp $(WORKSRC)/src/i915_pciids.h $(WORKSRC)/../../../files/
	@cd files; ./generate-intel-udev-rule.sh $(GARVERSION)
	@$(MAKECOOKIE)
