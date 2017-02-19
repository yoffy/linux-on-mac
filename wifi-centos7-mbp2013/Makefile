# See https://wiki.centos.org/HowTos/Laptops/Wireless/Broadcom

.PHONY: all
all: bcmwl
	$(MAKE) -C hybrid-wl

.PHONY: clean
clean:
	$(MAKE) -C hybrid-wl clean

.PHONY: install
install:
	$(MAKE) -C hybrid-wl install
	depmod -ae
	modprobe wl

.PHONY: bcmwl
bcmwl: bcmwl/.git hybrid-wl/src
	cd bcmwl && git checkout .
	cd hybrid-wl && patch -p1 < ../bcmwl/debian/patches/0020-add-support-for-linux-4.3.patch
	cd hybrid-wl && patch -p1 < ../bcmwl/debian/patches/0021-add-support-for-Linux-4.7.patch
	cd hybrid-wl && patch -p1 < ../bcmwl/debian/patches/0022-add-support-for-Linux-4.8.patch

bcmwl/.git:
	git clone https://github.com/tseliot/bcmwl.git

hybrid-wl/src: hybrid-v35_64-nodebug-pcoem-6_30_223_271.tar.gz
	mkdir -p hybrid-wl
	tar xf hybrid-v35_64-nodebug-pcoem-6_30_223_271.tar.gz -C hybrid-wl

hybrid-v35_64-nodebug-pcoem-6_30_223_271.tar.gz:
	@echo Driver found at http://www.broadcom.com/support/802.11/linux_sta.php
	exit 1
