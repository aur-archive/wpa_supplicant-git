# Maintainer: Pascal Ernster <aur (AT) archlinux (DOT) org>
# Contributor: Thomas Bächler <thomas@archlinux.org>

_pkgname=wpa_supplicant
pkgname="${_pkgname}-git"
pkgver=2_4.r250.g38ad1ea
pkgrel=1
pkgdesc="A utility providing key negotiation for WPA wireless networks"
url="http://hostap.epitest.fi/wpa_supplicant"
arch=('i686' 'x86_64')
depends=('openssl' 'libdbus' 'readline' 'libnl')
optdepends=('wpa_supplicant_gui: wpa_gui program')
makedepends=('asciidoc' 'docbook2x')
provides=('wpa_supplicant')
conflicts=('wpa_supplicant')
replaces=('wpa_supplicant')
license=('GPL')
backup=('etc/wpa_supplicant/wpa_supplicant.conf')
source=("git://w1.fi/hostap.git"
	config)
sha256sums=('SKIP'
            '15be07f0d8003a2239dfba008f09602f1203bbf143004d8979ff4d863153c817')

pkgver() {
	cd "hostap"
	git describe --long | sed 's/^hostap_//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
	cd "${srcdir}/hostap/${_pkgname}/"
	cp "${srcdir}/config" ./.config
}

build() {
	cd "${srcdir}/hostap/${_pkgname}/"
	
	# The Makefile does not pick up our CPPFLAGS
	export CFLAGS="$CPPFLAGS $CFLAGS"
	make LIBDIR=/usr/lib BINDIR=/usr/bin
}

package() {
	cd "${srcdir}/hostap/${_pkgname}/"
	make LIBDIR=/usr/lib BINDIR=/usr/bin DESTDIR="${pkgdir}" install
	
	install -d -m755 "${pkgdir}/etc/wpa_supplicant"
	install -m644 wpa_supplicant.conf "${pkgdir}/etc/wpa_supplicant/wpa_supplicant.conf"
	
	cd doc/docbook
	sed -i 's|<!doctype refentry PUBLIC "-//OASIS//DTD DocBook V4\.1//EN">|<?xml version="1.0" encoding="utf-8"?>\n<!DOCTYPE refentry PUBLIC "-//OASIS//DTD DocBook V4.5//EN" "http://www.oasis-open.org/docbook/xml/4.5/docbookx.dtd">|' *.sgml
	make man
	cd ../..
	
	install -d -m755 "${pkgdir}/usr/share/man/man"{5,8}
	install -m644 doc/docbook/*.5 "${pkgdir}/usr/share/man/man5/"
	install -m644 doc/docbook/*.8 "${pkgdir}/usr/share/man/man8/"
	rm -f "${pkgdir}/usr/share/man/man8/wpa_"{priv,gui}.8
	
	install -d -m755 "${pkgdir}/usr/share/dbus-1/system-services"
	install -m644 dbus/{fi.epitest.hostap.WPASupplicant.service,fi.w1.wpa_supplicant1.service} "${pkgdir}/usr/share/dbus-1/system-services/"
	
	install -d -m755 "${pkgdir}/etc/dbus-1/system.d"
	install -m644 dbus/dbus-wpa_supplicant.conf "${pkgdir}/etc/dbus-1/system.d/wpa_supplicant.conf"
	
	install -d -m755 "${pkgdir}/usr/lib/systemd/system"
	install -m644 systemd/*.service "${pkgdir}/usr/lib/systemd/system/"
}
