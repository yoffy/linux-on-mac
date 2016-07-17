# Release:
#     https://01.org/linuxgraphics/downloads/2016q2-intel-graphics-stack-release
# Documentation:
#     https://01.org/linuxgraphics/documentation/build-guide-0

MY_DISTRO_PREFIX=/usr
MY_DISTRO_LIBDIR=/usr/lib64
MY_DISTRO_INCLUDE=/usr/include
LINUX=linux-4.6
LIBDRM=libdrm-2.4.68
XORG_SERVER=xorg-server-1.18.3
XPROTO=xproto-7.0.29
RANDRPROTO=randrproto-1.5.0
XF86_INPUT_EVDEV=xf86-input-evdev-2.10.3
XF86_INPUT_SYNAPTICS=xf86-input-synaptics-1.8.99.1
XF86_VIDEO_INTEL=xf86-video-intel
XF86_VIDEO_NOUVEAU=xf86-video-nouveau-1.0.12
XF86_VIDEO_FBDEV=xf86-video-fbdev-0.4.4
XF86_VIDEO_VESA=xf86-video-vesa-2.3.4
XF86_VIDEO_NV=xf86-video-nv-2.1.20
MESA=mesa-11.2.2
LIBVA=libva-1.7.1
LIBVA_INTEL_DRIVER=libva-intel-driver-1.7.1
CAIRO=cairo
INTEL_GPU_TOOLS=intel-gpu-tools-1.15

LINUX_VERSION_O=$(LINUX)/modules.builtin
LIBDRM_VERSION_O=$(LIBDRM)/libdrm.la
XORG_SERVER_VERSION_O=$(XORG_SERVER)/xorg-server.pc
XPROTO_VERSION_O=$(XPROTO)/xproto.pc
RANDRPROTO_VERSION_O=$(RANDRPROTO)/randrproto.pc
XF86_INPUT_EVDEV_VERSION_O=$(XF86_INPUT_EVDEV)/src/evdev.lo
XF86_INPUT_SYNAPTICS_VERSION_O=$(XF86_INPUT_SYNAPTICS)/src/synaptics.lo
XF86_VIDEO_INTEL_VERSION_O=$(XF86_VIDEO_INTEL)/src/intel_drv.la
XF86_VIDEO_NOUVEAU_VERSION_O=$(XF86_VIDEO_NOUVEAU)/src/nouveau.lo
XF86_VIDEO_FBDEV_VERSION_O=$(XF86_VIDEO_FBDEV)/src/fbdev.lo
XF86_VIDEO_VESA_VERSION_O=$(XF86_VIDEO_VESA)/src/vesa.lo
XF86_VIDEO_NV_VERSION_O=$(XF86_VIDEO_NV)/src/nv.lo
MESA_VERSION_O=$(MESA)/src/egl/libEGL.la
LIBVA_VERSION_O=$(LIBVA)/va/va.lo
LIBVA_INTEL_DRIVER_VERSION_O=$(LIBVA_INTEL_DRIVER)/src/i965_drv_video.la
CAIRO_VERSION_O=$(CAIRO)/src/libcairo.la
INTEL_GPU_TOOLS_VERSION_O=$(INTEL_GPU_TOOLS)/lib/libintel_tools.la

.PHONY: all
all:
	if [ "`uname -r`" != "4.6.0" ]; then echo -e "Run following:\n\tmake kernel\n\tsudo make install-kernel\n\tsudo reboot"; exit 1; fi
	echo -e "Run following:\n\tmake install"

.PHONY: install
install:
	$(MAKE) libdrm                 && sudo $(MAKE) install-libdrm
	$(MAKE) xproto                 && sudo $(MAKE) install-xproto
	$(MAKE) randrproto             && sudo $(MAKE) install-randrproto
	$(MAKE) xorg-server            && sudo $(MAKE) install-xorg-server
	$(MAKE) xf86-input-evdev       && sudo $(MAKE) install-xf86-input-evdev
	$(MAKE) xf86-input-synaptics   && sudo $(MAKE) install-xf86-input-synaptics
	$(MAKE) xf86-video-intel       && sudo $(MAKE) install-xf86-video-intel
	$(MAKE) xf86-video-nouveau     && sudo $(MAKE) install-xf86-video-nouveau
	$(MAKE) xf86-video-fbdev       && sudo $(MAKE) install-xf86-video-fbdev
	$(MAKE) xf86-video-vesa        && sudo $(MAKE) install-xf86-video-vesa
	$(MAKE) xf86-video-nv          && sudo $(MAKE) install-xf86-video-nv
	$(MAKE) mesa                   && sudo $(MAKE) install-mesa
	$(MAKE) libva                  && sudo $(MAKE) install-libva
	$(MAKE) libva-intel-driver     && sudo $(MAKE) install-libva-intel-driver
	$(MAKE) cairo                  && sudo $(MAKE) install-cairo
	$(MAKE) intel-gpu-tools        && sudo $(MAKE) install-intel-gpu-tools

#
# kernel
#

.PHONY: kernel
kernel: $(LINUX_VERSION_O)

.PHONY: install-kernel
install-kernel: $(LINUX_VERSION_O)
	cd $(LINUX) \
	&& $(MAKE) modules_install \
	&& $(MAKE) install

$(LINUX_VERSION_O): $(MY_DISTRO_INCLUDE)/openssl/opensslv.h $(LINUX)/.config
	cd $(LINUX) \
	&& $(MAKE) \
	&& $(MAKE) modules

$(MY_DISTRO_INCLUDE)/openssl/opensslv.h:
	yum list installed openssl-devel > /dev/null 2>&1 || (echo sudo yum install openssl-devel && exit 1)

$(LINUX)/.config: $(LINUX).tar.xz
	tar xf $(LINUX).tar.xz
	cp `ls -t /boot/config* | head -1` "$(LINUX)/.config"

$(LINUX).tar.xz:
	curl -LOS# "https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.6.tar.xz"

.PHONY: patch-kernel-mac
patch-kernel-mac: $(LINUX)/.config
	cp kernel-config-mac $(LINUX)/.config
	curl -LS# -o kernel-eboot.patch   "https://lists.gnu.org/archive/html/grub-devel/2014-11/txtf1YcLj5EQ1.txt"
	curl -LS# -o kernel-9099221.patch "https://patchwork.kernel.org/patch/9099221/raw/"
	curl -LS# -o kernel-9099251.patch "https://patchwork.kernel.org/patch/9099251/raw/"
	curl -LS# -o kernel-9099271.patch "https://patchwork.kernel.org/patch/9099271/raw/"
	patch     -d $(LINUX) < kernel-eboot.patch
	patch -p1 -d $(LINUX) < kernel-9099221.patch
	patch -p1 -d $(LINUX) < kernel-9099251.patch
	patch -p1 -d $(LINUX) < kernel-9099271.patch
	touch $(LINUX)/.config
	echo "Add acpi_block_gpe=0x06 to boot parameter (/etc/grub2-efi.config etc.)"

#
# libdrm
#

.PHONY: libdrm
libdrm: $(LIBDRM_VERSION_O)

.PHONY: install-libdrm
install-libdrm: $(LIBDRM_VERSION_O)
	$(MAKE) -C $(LIBDRM) install

$(LIBDRM_VERSION_O): $(MY_DISTRO_LIBDIR)/pkgconfig/pciaccess.pc $(LIBDRM)/configure
	cd $(LIBDRM) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(LIBDRM)/configure: /usr/share/pkgconfig/xorg-macros.pc $(LIBDRM).tar.gz
	tar xf $(LIBDRM).tar.gz
	cd $(LIBDRM) \
	&& sed -i "/pthread-stubs/d" configure.ac \
	&& autoreconf -fiv

$(LIBDRM).tar.gz:
	curl -LOS# "http://dri.freedesktop.org/libdrm/libdrm-2.4.68.tar.gz"

$(MY_DISTRO_LIBDIR)/pkgconfig/pciaccess.pc:
	yum list installed libpciaccess-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install libpciaccess-devel" && exit 1)

/usr/share/pkgconfig/xorg-macros.pc:
	yum list installed xorg-x11-util-macros > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install xorg-x11-util-macros" && exit 1)

#
# xf86-input-evdev
#

.PHONY: xf86-input-evdev
xf86-input-evdev: $(XF86_INPUT_EVDEV_VERSION_O)

.PHONY: install-xf86-input-evdev
install-xf86-input-evdev: $(XF86_INPUT_EVDEV_VERSION_O)
	$(MAKE) -C $(XF86_INPUT_EVDEV) install

$(XF86_INPUT_EVDEV_VERSION_O): $(XF86_INPUT_EVDEV)/configure
	cd $(XF86_INPUT_EVDEV) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XF86_INPUT_EVDEV)/configure: \
	$(MY_DISTRO_LIBDIR)/pkgconfig/libevdev.pc \
	$(MY_DISTRO_LIBDIR)/pkgconfig/mtdev.pc \
	$(XF86_INPUT_EVDEV).tar.bz2
	tar xf $(XF86_INPUT_EVDEV).tar.bz2
	touch $(XF86_INPUT_EVDEV)/configure

$(XF86_INPUT_EVDEV).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/driver/$(XF86_INPUT_EVDEV).tar.bz2"

$(MY_DISTRO_LIBDIR)/pkgconfig/libevdev.pc:
	yum list installed libevdev-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install libevdev-devel" && exit 1)

$(MY_DISTRO_LIBDIR)/pkgconfig/mtdev.pc:
	yum list installed mtdev-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install mtdev-devel" && exit 1)

#
# xf86-input-synaptics
#

.PHONY: xf86-input-synaptics
xf86-input-synaptics: $(XF86_INPUT_SYNAPTICS_VERSION_O)

.PHONY: install-xf86-input-synaptics
install-xf86-input-synaptics: $(XF86_INPUT_SYNAPTICS_VERSION_O)
	$(MAKE) -C $(XF86_INPUT_SYNAPTICS) install

$(XF86_INPUT_SYNAPTICS_VERSION_O): \
	$(MY_DISTRO_LIBDIR)/pkgconfig/xi.pc \
	$(XF86_INPUT_SYNAPTICS)/configure
	cd $(XF86_INPUT_SYNAPTICS) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XF86_INPUT_SYNAPTICS)/configure: $(XF86_INPUT_SYNAPTICS).tar.bz2
	tar xf $(XF86_INPUT_SYNAPTICS).tar.bz2
	touch $(XF86_INPUT_SYNAPTICS)/configure

$(XF86_INPUT_SYNAPTICS).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/driver/$(XF86_INPUT_SYNAPTICS).tar.bz2"

$(MY_DISTRO_LIBDIR)/pkgconfig/xi.pc:
	yum list installed libXi-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install libXi-devel" && exit 1)

#
# xf86-video-intel
#

.PHONY: xf86-video-intel
xf86-video-intel: $(XF86_VIDEO_INTEL_VERSION_O)

.PHONY: install-xf86-video-intel
install-xf86-video-intel: $(XF86_VIDEO_INTEL_VERSION_O)
	$(MAKE) -C $(XF86_VIDEO_INTEL) install

$(XF86_VIDEO_INTEL_VERSION_O): \
	$(MY_DISTRO_LIBDIR)/pkgconfig/xorg-server.pc \
	$(XF86_VIDEO_INTEL)/configure
	cd $(XF86_VIDEO_INTEL) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XF86_VIDEO_INTEL)/configure:
	git clone git://anongit.freedesktop.org/xorg/driver/xf86-video-intel
	cd $(XF86_VIDEO_INTEL) && git checkout 26f8ab5429a554801641415f0ab3b2d68cea1d00
	touch $(XF86_VIDEO_INTEL)/configure

$(MY_DISTRO_LIBDIR)/pkgconfig/xorg-server.pc:
	yum list installed xorg-x11-server-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install xorg-x11-server-devel" && exit 1)

#
# xf86-video-fbdev
#

.PHONY: xf86-video-fbdev
xf86-video-fbdev: $(XF86_VIDEO_FBDEV_VERSION_O)

.PHONY: install-xf86-video-fbdev
install-xf86-video-fbdev: $(XF86_VIDEO_FBDEV_VERSION_O)
	$(MAKE) -C $(XF86_VIDEO_FBDEV) install

$(XF86_VIDEO_FBDEV_VERSION_O): $(XF86_VIDEO_FBDEV)/configure
	cd $(XF86_VIDEO_FBDEV) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XF86_VIDEO_FBDEV)/configure: $(XF86_VIDEO_FBDEV).tar.bz2
	tar xf $(XF86_VIDEO_FBDEV).tar.bz2
	touch $(XF86_VIDEO_FBDEV)/configure

$(XF86_VIDEO_FBDEV).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/driver/$(XF86_VIDEO_FBDEV).tar.bz2"

#
# xf86-video-vesa
#

.PHONY: xf86-video-vesa
xf86-video-vesa: $(XF86_VIDEO_VESA_VERSION_O)

.PHONY: install-xf86-video-vesa
install-xf86-video-vesa: $(XF86_VIDEO_VESA_VERSION_O)
	$(MAKE) -C $(XF86_VIDEO_VESA) install

$(XF86_VIDEO_VESA_VERSION_O): $(XF86_VIDEO_VESA)/configure
	cd $(XF86_VIDEO_VESA) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XF86_VIDEO_VESA)/configure: $(XF86_VIDEO_VESA).tar.bz2
	tar xf $(XF86_VIDEO_VESA).tar.bz2
	touch $(XF86_VIDEO_VESA)/configure

$(XF86_VIDEO_VESA).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/driver/$(XF86_VIDEO_VESA).tar.bz2"

#
# xf86-video-nv
#

.PHONY: xf86-video-nv
xf86-video-nv: $(XF86_VIDEO_NV_VERSION_O)

.PHONY: install-xf86-video-nv
install-xf86-video-nv: $(XF86_VIDEO_NV_VERSION_O)
	$(MAKE) -C $(XF86_VIDEO_NV) install

$(XF86_VIDEO_NV_VERSION_O): $(XF86_VIDEO_NV)/configure
	cd $(XF86_VIDEO_NV) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XF86_VIDEO_NV)/configure: $(XF86_VIDEO_NV).tar.bz2
	tar xf $(XF86_VIDEO_NV).tar.bz2
	# bug fix
	touch $(XF86_VIDEO_NV)/src/mibstore.h
	touch $(XF86_VIDEO_NV)/configure

$(XF86_VIDEO_NV).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/driver/$(XF86_VIDEO_NV).tar.bz2"

#
# mesa
#

.PHONY: mesa
mesa: $(MESA_VERSION_O)

.PHONY: install-mesa
install-mesa: $(MESA_VERSION_O)
	$(MAKE) -C $(MESA) install

$(MESA_VERSION_O): \
	$(MY_DISTRO_LIBDIR)/pkgconfig/libudev.pc \
	$(MY_DISTRO_LIBDIR)/pkgconfig/expat.pc \
	$(MESA)/autogen.sh
	$(MAKE) mako
	# ./autogen.sh --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) --with-dri-drivers="i915 i965 nouveau" --with-dri-driverdir=$(MY_DISTRO_LIBDIR)/dri --enable-gles1--enable-gles2 --enable-shared-glapi --with-gallium-drivers= --with-egl-platforms=x11,drm --enable-texture-float --enable-gbm --enable-glx-tls --enable-dri3
	cd $(MESA) \
	&& ./autogen.sh --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) --with-dri-driverdir=$(MY_DISTRO_LIBDIR)/dri --enable-gles1--enable-gles2 --enable-shared-glapi --with-gallium-drivers= --with-egl-platforms=x11,drm --enable-texture-float --enable-gbm --enable-glx-tls --enable-dri3 \
	&& $(MAKE)

$(MESA)/autogen.sh: $(MESA).tar.xz
	tar xf $(MESA).tar.xz
	touch $(MESA)/autogen.sh

$(MESA).tar.xz:
	curl -LOS# "ftp://ftp.freedesktop.org/pub/mesa/11.2.2/mesa-11.2.2.tar.xz"

$(MY_DISTRO_LIBDIR)/pkgconfig/libudev.pc:
	yum list installed systemd-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install systemd-devel" && exit 1)

$(MY_DISTRO_LIBDIR)/pkgconfig/expat.pc:
	yum list installed expat-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install expat-devel" && exit 1)

# for noveau (GeForce)
.PHONY: mako
mako: pip
	pip show mako || (echo -e "Run following:\n\tsudo pip install mako" && exit 1)

.PHONY: pip
pip:
	which pip || (echo -e "Run following:\n\tcurl -kL https://bootstrap.pypa.io/get-pip.py | sudo python" && exit 1)

#
# xorg-server
#

# install manually:
# - make xproto && sudo make install-xproto
# - make randrproto && sudo make install-randrproto

.PHONY: xorg-server
xorg-server: $(XORG_SERVER_VERSION_O)

.PHONY: install-xorg-server
install-xorg-server: $(XORG_SERVER_VERSION_O)
	$(MAKE) -C $(XORG_SERVER) install

$(XORG_SERVER_VERSION_O): \
	$(MY_DISTRO_PREFIX)/share/pkgconfig/xtrans.pc \
	$(MY_DISTRO_LIBDIR)/pkgconfig/xkbfile.pc \
	$(MY_DISTRO_LIBDIR)/pkgconfig/xfont.pc \
	$(XORG_SERVER)/autogen.sh
	cd $(XORG_SERVER) \
	&& ./autogen.sh --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) --enable-config-udev --disable-config-hal --disable-unit-tests \
	&& $(MAKE)

$(XORG_SERVER)/autogen.sh: $(XORG_SERVER).tar.bz2
	tar xf $(XORG_SERVER).tar.bz2
	touch $(XORG_SERVER)/autogen.sh

$(XORG_SERVER).tar.bz2:
	curl -LOS# "http://ftp.x.org/pub/individual/xserver/xorg-server-1.18.3.tar.bz2"

$(MY_DISTRO_PREFIX)/share/pkgconfig/xtrans.pc:
	yum list installed xorg-x11-xtrans-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install xorg-x11-xtrans-devel" && exit 1)


$(MY_DISTRO_LIBDIR)/pkgconfig/xkbfile.pc:
	yum list installed libxkbfile-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install libxkbfile-devel" && exit 1)

$(MY_DISTRO_LIBDIR)/pkgconfig/xfont.pc:
	yum list installed libXfont-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install libXfont-devel" && exit 1)

#
# xproto
#

.PHONY: xproto
xproto: $(XPROTO_VERSION_O)

.PHONY: install-xproto
install-xproto: $(XPROTO_VERSION_O)
	$(MAKE) -C $(XPROTO) install

$(XPROTO_VERSION_O): $(XPROTO)/configure
	cd $(XPROTO) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XPROTO)/configure: $(XPROTO).tar.bz2
	tar xf $(XPROTO).tar.bz2
	touch $(XPROTO)/configure

$(XPROTO).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/proto/$(XPROTO).tar.bz2"

#
# randrproto
#

.PHONY: randrproto
randrproto: $(RANDRPROTO_VERSION_O)

.PHONY: install-randrproto
install-randrproto: $(RANDRPROTO_VERSION_O)
	$(MAKE) -C $(RANDRPROTO) install

$(RANDRPROTO_VERSION_O): $(RANDRPROTO)/configure
	cd $(RANDRPROTO) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(RANDRPROTO)/configure: $(RANDRPROTO).tar.bz2
	tar xf $(RANDRPROTO).tar.bz2
	touch $(RANDRPROTO)/configure

$(RANDRPROTO).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/proto/$(RANDRPROTO).tar.bz2"

#
# xf86-video-nouveau
#

.PHONY: xf86-video-nouveau
xf86-video-nouveau: $(XF86_VIDEO_NOUVEAU_VERSION_O)

.PHONY: install-xf86-video-nouveau
install-xf86-video-nouveau: $(XF86_VIDEO_NOUVEAU_VERSION_O)
	$(MAKE) -C $(XF86_VIDEO_NOUVEAU) install

$(XF86_VIDEO_NOUVEAU_VERSION_O): $(XF86_VIDEO_NOUVEAU)/configure
	cd $(XF86_VIDEO_NOUVEAU) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(XF86_VIDEO_NOUVEAU)/configure: $(XF86_VIDEO_NOUVEAU).tar.bz2
	tar xf $(XF86_VIDEO_NOUVEAU).tar.bz2
	touch $(XF86_VIDEO_NOUVEAU)/configure

$(XF86_VIDEO_NOUVEAU).tar.bz2:
	curl -LOS# "https://www.x.org/archive/individual/driver/$(XF86_VIDEO_NOUVEAU).tar.bz2"

#
# libva
#

.PHONY: libva
libva: $(LIBVA_VERSION_O)

.PHONY: install-libva
install-libva: $(LIBVA_VERSION_O)
	$(MAKE) -C $(LIBVA) install

$(LIBVA_VERSION_O): $(LIBVA)/configure
	cd $(LIBVA) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) --enable-wayland=0 \
	&& $(MAKE)

$(LIBVA)/configure: $(LIBVA).tar.bz2
	tar xf $(LIBVA).tar.bz2
	touch $(LIBVA)/configure

$(LIBVA).tar.bz2:
	curl -LOS# "http://www.freedesktop.org/software/vaapi/releases/libva/libva-1.7.1.tar.bz2"

#
# libva-intel-driver
#

.PHONY: libva-intel-driver
libva-intel-driver: $(LIBVA_INTEL_DRIVER_VERSION_O)

.PHONY: install-libva-intel-driver
install-libva-intel-driver: $(LIBVA_INTEL_DRIVER_VERSION_O)
	$(MAKE) -C $(LIBVA_INTEL_DRIVER) install

$(LIBVA_INTEL_DRIVER_VERSION_O): $(LIBVA_INTEL_DRIVER)/configure
	cd $(LIBVA_INTEL_DRIVER) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) --enable-wayland=0 \
	&& $(MAKE)

$(LIBVA_INTEL_DRIVER)/configure: $(LIBVA_INTEL_DRIVER).tar.bz2
	tar xf $(LIBVA_INTEL_DRIVER).tar.bz2
	touch $(LIBVA_INTEL_DRIVER)/configure

$(LIBVA_INTEL_DRIVER).tar.bz2:
	curl -LOS# "http://www.freedesktop.org/software/vaapi/releases/libva-intel-driver/libva-intel-driver-1.7.1.tar.bz2"

#
# cairo
#

.PHONY: cairo
cairo: $(CAIRO_VERSION_O)

.PHONY: install-cairo
install-cairo: $(CAIRO_VERSION_O)
	$(MAKE) -C $(CAIRO) install

$(CAIRO_VERSION_O): \
	$(MY_DISTRO_LIBDIR)/pkgconfig/libpng.pc \
	$(CAIRO)/autogen.sh
	cd $(CAIRO) \
	&& ./autogen.sh --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(CAIRO)/autogen.sh:
	git clone -b 1.15.2 https://anongit.freedesktop.org/git/cairo
	touch $(CAIRO)/autogen.sh

$(MY_DISTRO_LIBDIR)/pkgconfig/libpng.pc:
	yum list installed libpng-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install libpng-devel" && exit 1)

#
# intel-gpu-tools
#

.PHONY: intel-gpu-tools
intel-gpu-tools: $(INTEL_GPU_TOOLS_VERSION_O)

.PHONY: install-intel-gpu-tools
install-intel-gpu-tools: $(INTEL_GPU_TOOLS_VERSION_O)
	$(MAKE) -C $(INTEL_GPU_TOOLS) install

$(INTEL_GPU_TOOLS_VERSION_O): \
	$(MY_DISTRO_LIBDIR)/pkgconfig/cairo.pc \
	$(MY_DISTRO_LIBDIR)/pkgconfig/libunwind.pc \
	$(MY_DISTRO_LIBDIR)/pkgconfig/glib-2.0.pc \
	$(INTEL_GPU_TOOLS)/configure
	cd $(INTEL_GPU_TOOLS) \
	&& ./configure --prefix=$(MY_DISTRO_PREFIX) --libdir=$(MY_DISTRO_LIBDIR) \
	&& $(MAKE)

$(INTEL_GPU_TOOLS)/configure: $(INTEL_GPU_TOOLS).tar.bz2
	tar xf $(INTEL_GPU_TOOLS).tar.bz2
	touch $(INTEL_GPU_TOOLS)/configure

$(INTEL_GPU_TOOLS).tar.bz2:
	curl -LOS# "http://xorg.freedesktop.org/archive/individual/app/intel-gpu-tools-1.15.tar.bz2"

$(MY_DISTRO_LIBDIR)/pkgconfig/cairo.pc:
	echo -e "Run following:\n\tmake cairo && sudo make install-cairo" && exit 1

$(MY_DISTRO_LIBDIR)/pkgconfig/libunwind.pc:
	yum list installed libunwind-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install libunwind-devel" && exit 1)

$(MY_DISTRO_LIBDIR)/pkgconfig/glib-2.0.pc:
	yum list installed glib2-devel > /dev/null 2>&1 || (echo -e "Run following:\n\tsudo yum install glib2-devel" && exit 1)
