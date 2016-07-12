# Maintainer: James An <james@jamesan.ca>
# Contributor: AwesomeHaircut <jesusbalbastro at gmail com>

pkgname=droidcam
pkgver=6.0
pkgrel=6
pkgdesc='A tool for using your android device as a wireless/usb webcam'
arch=('x86_64')
url='http://www.dev47apps.com/'
license=('custom')
depends=( 'bluez-libs' 'gtk2')
makedepends=( 'linux-headers' )
options=('!strip')
optdepends=('v4l-utils: Userspace tools and conversion library for Video 4 Linux'
            'xf86-video-v4l: X.org v4l video driver' )
install="$pkgname.install"

source=("$pkgname.desktop"
        "https://github.com/aramg/$pkgname/raw/master/linux/icon2.png"
        "https://www.dev47apps.com/files/600/$pkgname-64bit.tar.bz2")
md5sums=('199d8f3dbc6697f06350b00de99f2274'
         '0f0e1d04146dd5be70d5028f144bd0a2'
         '743b71f1af4d90b5ced59c02fcbc925f')

prepare() {
  # Generate the module loading configuration files
  cat <<EOF >| "$pkgname.modules-load.conf"
videodev
v4l2loopback
v4l2loopback_dc
EOF
  echo "options v4l2loopback_dc width=320 height=240" >| "$pkgname.modprobe.conf"
}

build() {
  cd "$pkgname-64bit/v4l2loopback"
  # Build kernel module
  sed -i -e "s,vdev->current_norm,//vdev->current_norm,g" *.c
  make
  gzip -f v4l2loopback-dc.ko
}

package() {
  # Install droidcam binary file
  cd $pkgname-64bit
  mkdir -p "$pkgdir"/usr/bin
  install -m755 ${pkgname} "$pkgdir"/usr/bin/${pkgname}
  install -m755 ${pkgname}-cli "$pkgdir"/usr/bin/${pkgname}-cli

  # Install the desktop icon and ".desktop" files
  install -dm0755 "${pkgdir}/usr/share/"{applications,pixmaps}
  install -m0644 "${srcdir}/icon2.png" "${pkgdir}/usr/share/pixmaps/${pkgname}.png"
  install -m0644 "${srcdir}/${pkgname}.desktop" "${pkgdir}/usr/share/applications/${pkgname}.desktop"

  install -Dm644 "${srcdir}/$pkgname.modules-load.conf" "$pkgdir/usr/lib/modules-load.d/$pkgname.conf"
  install -Dm644 "${srcdir}/$pkgname.modprobe.conf"     "$pkgdir/etc/modprobe.d/$pkgname.conf"

  # Install doc
  install -dm0755 "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m0644 README "${pkgdir}/usr/share/licenses/$pkgname/README"

  # Install kernel module
  cd v4l2loopback
  _extramodules="extramodules-$(uname -r | cut -f-2 -d'.')-$(uname -r|sed -e 's/.*-//g')"
  MODPATH="${pkgdir}/usr/lib/modules/${_extramodules}/"
  install -Dm644 v4l2loopback-dc.ko.gz "$MODPATH/v4l2loopback_dc.ko.gz"
}
