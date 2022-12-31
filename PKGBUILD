# U-Boot: RockPro64
# Maintainer: Goksel Kabadayi <gokselkabadayi@gmail.com>

buildarch=8

pkgname=uboot-rockpro64
pkgver=2022.10
pkgrel=1
pkgdesc="U-Boot for RockPro64"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
backup=('boot/boot.txt' 'boot/boot.scr')
makedepends=('bc' 'git' 'python' 'swig' 'dtc' 'uboot-tools')
install=${pkgname}.install
source=("ftp://ftp.denx.de/pub/u-boot/u-boot-${pkgver}.tar.bz2"
        "https://git.trustedfirmware.org/TF-A/trusted-firmware-a.git/snapshot/trusted-firmware-a-2.8.tar.gz"
        'boot.txt'
        'mkscr'
        '0001-mmc-sdhci-allow-disabling-sdma-in-spl.patch')
md5sums=('cb1f3774f5e423b91cff379101c764a3'
         '9671bd2898aa11e57c3b573252a10920'
         '5631cbd1f70685ff49a55c94e710ba97'
         '021623a04afd29ac3f368977140cfbfd'
         'dbd251d5c68c14825483da2fa2d5898b')

prepare() {
  cd "${srcdir}/u-boot-${pkgver}"
  patch -N -p1 -i "${srcdir}/0001-mmc-sdhci-allow-disabling-sdma-in-spl.patch"

  cd "${srcdir}/trusted-firmware-a-2.8"
  make PLAT=rk3399 all

  cd "${srcdir}/u-boot-${pkgver}"
  cp "../trusted-firmware-a-2.8/build/rk3399/release/bl31/bl31.elf" ./bl31.elf

  cd "${srcdir}/u-boot-${pkgver}/configs"
  echo 'CONFIG_IDENT_STRING=" Arch Linux ARM"' >> rockpro64-rk3399_defconfig
}

build() {
  cd "${srcdir}/u-boot-${pkgver}"

  unset CFLAGS CXXFLAGS CPPFLAGS LDFLAGS

  make rockpro64-rk3399_defconfig
  make EXTRAVERSION=-${pkgrel}
  make EXTRAVERSION=-${pkgrel} u-boot.itb
}

package() {
  cd u-boot-${pkgver}

  mkdir -p "${pkgdir}/boot"

  tools/mkimage -n rk3399 -T rksd -d ./tpl/u-boot-tpl.bin "${pkgdir}/boot/rksd_loader.img"
  cat ./spl/u-boot-spl.bin >> "${pkgdir}/boot/rksd_loader.img"

  cp ./u-boot.itb "${pkgdir}/boot/u-boot.itb"

  tools/mkimage -A arm -O linux -T script -C none -n "U-Boot boot script" -d "../boot.txt" "${pkgdir}/boot/boot.scr"
  cp ../{boot.txt,mkscr} "${pkgdir}"/boot
}
