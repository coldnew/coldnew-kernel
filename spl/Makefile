subdir-m += spl


INSTALL_MOD_DIR ?= extra

SPL_MODULE_CFLAGS  = -I/var/tmp/portage/sys-kernel/spl-0.6.4-r1/work/spl-spl-0.6.4.1/include
SPL_MODULE_CFLAGS += -include /var/tmp/portage/sys-kernel/spl-0.6.4-r1/work/spl-spl-0.6.4.1/spl_config.h
export SPL_MODULE_CFLAGS

modules:
	$(MAKE) -C /usr/src/linux SUBDIRS=`pwd`  CONFIG_SPL=m $@

clean:
	@# Only cleanup the kernel build directories when CONFIG_KERNEL
	@# is defined.  This indicates that kernel modules should be built.
#	$(MAKE) -C /usr/src/linux SUBDIRS=`pwd`  $@

	if [ -f NONE ]; then $(RM) NONE; fi
	if [ -f Module.markers ]; then $(RM) Module.markers; fi

modules_install:
	@# Install the kernel modules
	$(MAKE) -C /usr/src/linux SUBDIRS=`pwd` $@ \
		INSTALL_MOD_PATH=$(DESTDIR)$(INSTALL_MOD_PATH) \
		INSTALL_MOD_DIR=$(INSTALL_MOD_DIR) \
		KERNELRELEASE=4.1.0-rc1-00006-g422cef4
	@# Remove extraneous build products when packaging
	kmoddir=$(DESTDIR)$(INSTALL_MOD_PATH)/lib/modules/4.1.0-rc1-00006-g422cef4; \
	if [ -n $$kmoddir ]; then \
		find $$kmoddir -name 'modules.*' | xargs $(RM); \
	fi
	sysmap=$(DESTDIR)$(INSTALL_MOD_PATH)/boot/System.map-4.1.0-rc1-00006-g422cef4; \
	if [ -f $$sysmap ]; then \
		depmod -ae -F $$sysmap 4.1.0-rc1-00006-g422cef4; \
	fi

modules_uninstall:
	@# Uninstall the kernel modules
	kmoddir=$(DESTDIR)$(INSTALL_MOD_PATH)/lib/modules/4.1.0-rc1-00006-g422cef4
	list='$(subdir-m)'; for subdir in $$list; do \
		$(RM) -R $$kmoddir/$(INSTALL_MOD_DIR)/$$subdir; \
	done

distdir:
	list='$(subdir-m)'; for subdir in $$list; do \
		(find ../module/$$subdir -name '*.c' -o -name '*.h' |\
		 xargs /bin/cp -t $$distdir/$$subdir); \
	done

distclean maintainer-clean: clean
install: modules_install
uninstall: modules_uninstall
all: modules
check:
