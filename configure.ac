# Process this file with autoconf to produce a configure script.
AC_PREREQ(2.59)

AC_INIT([wios], [1.0.0], [wios@591wifi.com], [wios], [http://wios.591wifi.com])
AC_COPYRIGHT([Copyright (c) 2017 591wifi.com])

# Checks for programs.
AC_PROG_LN_S

# Checks for header files.

# Checks for typedefs, structures, and compiler characteristics.

# process argument variables
test "X$LB_ARCHITECTURE" = "X" && LB_ARCHITECTURE=`dpkg --print-architecture`
AC_ARG_VAR(LB_ARCHITECTURE, [default: dpkg --print-architecture])

test "X$LB_APT" = "X" && LB_APT=aptitude
AC_ARG_VAR(LB_APT, [default: aptitude])

test ! "$LB_BOOTAPPEND_LIVE" && LB_BOOTAPPEND_LIVE=""
AC_ARG_VAR(LB_BOOTAPPEND_LIVE,
	[empty by default since bypassed with custom config file])

test ! "$LB_BOOTAPPEND_INSTALL" && LB_BOOTAPPEND_INSTALL=""
AC_ARG_VAR(LB_BOOTAPPEND_INSTALL,
	[empty by default since bypassed with custom config file])

test ! "$LB_BOOTLOADER" && LB_BOOTLOADER=syslinux
AC_ARG_VAR(LB_BOOTLOADER, [default: syslinux])

test "X$LB_BOOTSTRAP" = "X" && LB_BOOTSTRAP=debootstrap
AC_ARG_VAR(LB_BOOTSTRAP, [default: debootstrap])

test "X$LB_BOOTSTRAP_FLAVOUR" = "X" && LB_BOOTSTRAP_FLAVOUR="minimal"
AC_ARG_VAR(LB_BOOTSTRAP_FLAVOUR, [default: minimal])

test "X$LB_CHROOT_FILESYSTEM" = "X" && LB_CHROOT_FILESYSTEM=squashfs
AC_ARG_VAR(LB_CHROOT_FILESYSTEM, [default: squashfs])

test "X$LB_HOSTNAME" = "X" && LB_HOSTNAME=wios
AC_ARG_VAR(LB_HOSTNAME, [default: wios])

test "X$LB_INITRAMFS" = "X" && LB_INITRAMFS=live-initramfs
AC_ARG_VAR(LB_INITRAMFS, [default: live-initramfs])

test "X$LB_ISO_APPLICATION" = "X" && LB_ISO_APPLICATION="WiOS"
AC_ARG_VAR(LB_ISO_APPLICATION, [default: WiOS])

test "X$LB_ISO_PUBLISHER" = "X" && LB_ISO_PUBLISHER="WiOS; http://wios.591wifi.com/;"
AC_ARG_VAR(LB_ISO_PUBLISHER, [default: WiOS])

test "X$LB_ISO_VOLUME" = "X" && LB_ISO_VOLUME='WiOS '
AC_ARG_VAR(LB_ISO_VOLUME, [default: WiOS])

test "X$LB_LANGUANGE" = "X" && LB_LANGUAGE=en
AC_ARG_VAR(LB_LANGUAGE, [default: en])

test "X$LB_MEMTEST" = "X" && LB_MEMTEST=none
AC_ARG_VAR(LB_MEMTEST, [default: none])

test "X$LB_PACKAGES" = "X" && LB_PACKAGES=none
AC_ARG_VAR(LB_PACKAGES, [default: none])

test "X$LB_SYSLINUX_CFG" = "X" && LB_SYSLINUX_CFG=templates/isolinux.cfg
AC_ARG_VAR(LB_SYSLINUX_CFG, [default: templates/isolinux.cfg])

test "X$LB_SYSLINUX_MENU" = "X" && LB_SYSLINUX_MENU=false
AC_ARG_VAR(LB_SYSLINUX_MENU, [default: false])

test "X$LB_SYSLINUX_TIMEOUT" = "X" && LB_SYSLINUX_TIMEOUT=50
AC_ARG_VAR(LB_SYSLINUX_TIMEOUT, [default: 50, 1/10 seconds])

# process --with[out] arguments
AC_ARG_WITH(target-dist,
	[AS_HELP_STRING([--with-target-dist=<dist>],
	  [Target distribution (e.g., "squeeze"), default to the host distribution])],
	[],
	[with_target_dist=$(lsb_release -c -s)])

if test "$with_target_dist" = "n/a"; then
  LB_DISTRIBUTION=squeeze
else
  LB_DISTRIBUTION="$with_target_dist"
fi

AC_ARG_WITH(devbuild,
	[AS_HELP_STRING([--without-devbuild],
	  [disable development build])],
	[],
	[with_devbuild=yes])

if test "X$with_devbuild" = Xyes ; then
  WIOS_DEV_BUILD=true

  # For the case previous build was a release build
  rm -f livecd/version
else
  WIOS_DEV_BUILD=false
fi

AC_ARG_WITH(release-build,
	[AS_HELP_STRING([--with-release-build],
	  [enable release build])],
	[with_community_build=yes],
	[])
if test "X$with_community_build" = Xyes ; then
  WIOS_RELEASE_BUILD=true
  WIOS_DEV_BUILD=false
else
  WIOS_RELEASE_BUILD=false
fi

if eval "$WIOS_DEV_BUILD"; then
  touch pkgs/.WIOS_DEV_BUILD
else
  rm -f pkgs/.WIOS_DEV_BUILD
fi

AC_ARG_WITH(build-by,
  [AS_HELP_STRING([--with-build-by=<builder id>],
    [Specify builder id, normally your email (e.g. wios@591wifi.com])],
  [], [])

if test X$with_build_by != X ; then
  echo $with_build_by > livecd/buildby
else
  rm -f livecd/buildby
fi

# build arch
BARCH=$(dpkg-architecture -qDEB_BUILD_ARCH)

# default flavor for i386 and amd64
LB_LINUX_FLAVOURS="586-wios"
VIRT_FLAVOUR="586-wios-virt"
if test X$BARCH = Xamd64; then
  LB_LINUX_FLAVOURS="amd64-wios"
  VIRT_FLAVOUR="amd64-wios"
fi

# user-specified flavor
AC_ARG_WITH(kernel-flavor,
[AS_HELP_STRING([--with-kernel-flavor], [Build specified kernel flavor])])

if test X$with_kernel_flavor != X ; then
  LB_LINUX_FLAVOURS=$with_kernel_flavor
fi

AC_ARG_WITH(xen_pv_image,
[AS_HELP_STRING([--with-xen-pv-image], [Create a Xen PV image])])

if test X$with_xen_pv_image = Xyes ; then
  # use virt flavor for xen-pv-image build
  LB_LINUX_FLAVOURS=$VIRT_FLAVOUR
  create_xen_pv_image=true
else
  create_xen_pv_image=false
fi

# user specified PV boot device and image size

AC_ARG_WITH(xen_pv_boot_device,
[AS_HELP_STRING([--with-xen-pv-boot-device], [Set the Xen PV image boot device (default xvda)])])

if test X$with_xen_pv_boot_device != X ; then
  PV_BOOT_DEVICE=$with_xen_pv_boot_device
else
  PV_BOOT_DEVICE="xvda"
fi

AC_ARG_WITH(xen_pv_image_size,
[AS_HELP_STRING([--with-xen-pv-image-size], [Set the size of the Xen PV image (default 4G)])])

if test X$with_xen_pv_image_size != X ; then
  PV_IMAGE_SIZE=$with_xen_pv_image_size
else
  PV_IMAGE_SIZE="4G"
fi

# Expansion to allow definition of WIOS_BUILD_BRANCH
# on configure invocation
AC_ARG_WITH(wios_build_branch,
  [AS_HELP_STRING([--with-wios-build-branch=<branch>],
  [Target branch (e.g., "lithium"), default to the supermodule branch])],
  [])

if test "X$with_wios_build_branch" != X ; then
  echo BRANCH is $with_wios_build_branch
  WIOS_BUILD_BRANCH="$with_wios_build_branch"
fi

# Debian bootstrap package mirror
AC_ARG_WITH(debian_bootstrap_mirror,
  [AS_HELP_STRING([--with-debian-bootstrap-mirror=<URL>, relative to /debian directory],
  [Debian bootstrap package mirror URL])],
  [])

if test X$with_debian_bootstrap_mirror != X ; then
  echo "Using $with_debian_bootstrap_mirror as debian bootstrap mirror"
  WIOS_DEBIAN_BOOTSTRAP_MIRROR="$with_debian_bootstrap_mirror"
else
  WIOS_DEBIAN_BOOTSTRAP_MIRROR="http://dev.packages.591wifi.com"
fi

# Debian package mirror
AC_ARG_WITH(debian_mirror,
  [AS_HELP_STRING([--with-debian-mirror=<URL>, relative to /debian directory],
  [Debian package mirror URL])],
  [])

if test X$with_debian_mirror != X ; then
  echo "Using $with_debian_mirror as debian mirror"
  WIOS_DEBIAN_MIRROR="$with_debian_mirror"
else
  WIOS_DEBIAN_MIRROR="http://dev.packages.591wifi.com"
fi

# WIOS package mirror
AC_ARG_WITH(wios_mirror,
  [AS_HELP_STRING([--with-wios-mirror=<URL>, relative to branch directory],
  [WiOS package mirror URL])],
  [])

if test X$with_wios_mirror != X ; then
  echo "Using $with_wios_mirror as WiOS package mirror"
  WIOS_PACKAGE_MIRROR="$with_wios_mirror"
else
  WIOS_PACKAGE_MIRROR="http://dev.packages.591wifi.com/"
fi

BUILD_TIME=''
AC_ARG_WITH(build-time,
[AS_HELP_STRING([--with-build-time], [use specified build time])])
if test X$with_build_time != X; then
  BUILD_TIME=$with_build_time
fi

KERN_SUBMOD_DIR=$(pwd)/pkgs/linux-image
KERN_DEB_ARCHDEF_DIR=$KERN_SUBMOD_DIR/debian/arch
KERN_DEB_ARCH_DIR=$KERN_DEB_ARCHDEF_DIR/$BARCH

# only do these if we are building the kernel locally
if test -d $KERN_SUBMOD_DIR/debian; then
  if test ! -d $KERN_DEB_ARCH_DIR; then
    echo "Cannot build $BARCH kernel."
    exit 1
  fi

  if test ! -f $KERN_DEB_ARCH_DIR/defines.$LB_LINUX_FLAVOURS; then
    echo "Cannot build $BARCH-$LB_LINUX_FLAVOURS kernel."
    exit 1
  else
    cp -f $KERN_DEB_ARCH_DIR/defines.$LB_LINUX_FLAVOURS \
      $KERN_DEB_ARCH_DIR/defines
  fi

  # generate control file
  (
    cd $KERN_SUBMOD_DIR
    rm debian/{control,control.md5sum}
    make -f debian/rules debian/control || true
  )>&/dev/null
fi

LB_LINUX_PACKAGES=none

if test "X$WIOS_BUILD_BRANCH" == X; then
  WIOS_BUILD_BRANCH=$(git branch |sed -n 's/^\* \(.*\)$/\1/p')
fi

LB_SECURITY="false"
LB_SECTIONS="main"

AC_CONFIG_FILES([livecd/mk.livecd.conf])

AC_SUBST(ARCH)
AC_SUBST(CONFIG_BUILD)
AC_SUBST(CONFIG_HOST)
AC_SUBST(CONFIG_TARGET)
AC_SUBST(MACHINE_CFLAG)
AC_SUBST(MACHINE_PREFIX)
AC_SUBST(NOSTRIP)

AC_SUBST(LB_APT)
AC_SUBST(LB_ARCHITECTURE)
AC_SUBST(LB_BOOTAPPEND_LIVE)
AC_SUBST(LB_BOOTAPPEND_INSTALL)
AC_SUBST(LB_BOOTLOADER)
AC_SUBST(LB_BOOTSTRAP)
AC_SUBST(LB_BOOTSTRAP_FLAVOUR)
AC_SUBST(LB_CHROOT_FILESYSTEM)
AC_SUBST(LB_DISTRIBUTION)
AC_SUBST(LB_EXPOSED_ROOT)
AC_SUBST(LB_HOSTNAME)
AC_SUBST(LB_INITRAMFS)
AC_SUBST(LB_ISO_APPLICATION)
AC_SUBST(LB_ISO_PUBLISHER)
AC_SUBST(LB_ISO_VOLUME)
AC_SUBST(LB_LANGUAGE)
AC_SUBST(LB_LINUX_PACKAGES)
AC_SUBST(LB_LINUX_FLAVOURS)
AC_SUBST(LB_MEMTEST)
AC_SUBST(LB_PACKAGES)
AC_SUBST(LB_SECTIONS)
AC_SUBST(LB_SECURITY)
AC_SUBST(LB_SYSLINUX_CFG)
AC_SUBST(LB_SYSLINUX_MENU)
AC_SUBST(LB_SYSLINUX_TIMEOUT)
AC_SUBST(LB_UNION_FILESYSTEM)

AC_SUBST(create_xen_pv_image)
AC_SUBST(PV_BOOT_DEVICE)
AC_SUBST(PV_IMAGE_SIZE)

AC_SUBST(WIOS_DEBIAN_BOOTSTRAP_MIRROR)
AC_SUBST(WIOS_DEBIAN_MIRROR)
AC_SUBST(WIOS_PACKAGE_MIRROR)

AC_SUBST(WIOS_DEV_BUILD)
AC_SUBST(WIOS_RELEASE_BUILD)
AC_SUBST(WIOS_BUILD_BRANCH)
AC_SUBST(BUILD_TIME)

AC_OUTPUT
