subdir-m += avl
subdir-m += nvpair
subdir-m += unicode
subdir-m += zcommon
subdir-m += zfs


INSTALL_MOD_DIR ?= extra

ZFS_MODULE_CFLAGS += -include /usr/src/linux/spl_config.h
ZFS_MODULE_CFLAGS += -include /var/tmp/portage/sys-fs/zfs-kmod-0.6.4-r1/work/zfs-zfs-0.6.4.1/zfs_config.h
ZFS_MODULE_CFLAGS += -I/var/tmp/portage/sys-fs/zfs-kmod-0.6.4-r1/work/zfs-zfs-0.6.4.1/include -I/usr/src/linux/include -I/usr/src/linux
export ZFS_MODULE_CFLAGS

modules:
	@# Make the exported SPL symbols available to these modules.
	@# They may be in the root of SPL_OBJ when building against
	@# installed devel headers, or they may be in the module
	@# subdirectory when building against the spl source tree.
	@if [ -f /usr/src/linux/NONE ]; then \
		/bin/cp /usr/src/linux/NONE .; \
	elif [ -f /usr/src/linux/module/NONE ]; then \
		/bin/cp /usr/src/linux/module/NONE .; \
	else \
		echo -e "\n" \
		"*** Missing spl symbols ensure you have built the spl:\n" \
		"*** - /usr/src/linux/NONE, or\n" \
		"*** - /usr/src/linux/module/NONE\n"; \
		exit 1; \
	fi
	$(MAKE) -C /usr/src/linux SUBDIRS=`pwd`  CONFIG_ZFS=m $@

clean:
	@# Only cleanup the kernel build directories when CONFIG_KERNEL
	@# is defined.  This indicates that kernel modules should be built.
#	$(MAKE) -C /usr/src/linux SUBDIRS=`pwd`  $@

	if [ -f NONE ]; then $(RM) NONE; fi
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

distclean maintainer-clean: clean
install: modules_install
uninstall: modules_uninstall
all: modules
check:
