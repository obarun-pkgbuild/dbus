# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/dbus
# 						Maintainer: Jan de Groot <jgc@archlinux.org>
# 						Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# 						Contributor: Tom Gundersen <teg@jklm.no>
# 						Contributor: Link Dupont <link@subpop.net>
#

pkgbase=dbus
pkgname=('dbus' 'dbus-docs')
pkgver=1.10.24
pkgrel=2
pkgdesc="Freedesktop.org message bus system"
url="https://wiki.freedesktop.org/Software/dbus"
arch=(x86_64)
license=('GPL' 'custom')
depends=(expat)
conflicts=('dbus-eudev')
makedepends=('xmlto' 'docbook-xsl' 'python' 'yelp-tools' 'doxygen' 'git'
			'xtrans' 'libx11' 'libxext' 'libfs' 'libice' 'libsm' 'libxt'
			'libxmu' 'libxpm' 'libxaw' 'libxfixes' 'libxcomposite' 'libxrender'
			'libxcursor' 'libxdamage' 'libfontenc' 'libxfont' 'libxfont2'
			'libxft' 'libxi' 'libxinerama' 'libxrandr' 'libxres' 'libxtst'
			'libxv' 'libxvmc' 'libxxf86dga' 'libxxf86vm' 'libdmx' 'libpciaccess'
			'libxkbfile' 'libxshmfence' 'autoconf-archive')
_commit=430643da9da488a0b089868b9aada324f24a1710 # tags/dbus-1.10.24^0
source=("git+https://anongit.freedesktop.org/git/dbus/dbus#commit=$_commit"
        'dbus.sysusers'
        'dbus.tmpfiles')
sha256sums=('SKIP'
            '1ce179ba3a92ad34941d8ac7f53d01d42cbc91d43ada1136492b78c10b5d693d'
            '965cef20cce35819e89c65f06a931a38bea2119b0ae9c259b5d7f9cfc3edd6d7')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

pkgver() {
  cd $pkgbase
  git describe --tags | sed 's/^dbus-//;s/-/+/g'
}

prepare() {
  cd $pkgbase
  git cherry-pick -n 09cb6d7b467f6d1c6685ee9ccc171f4dddbe1f42
  NOCONFIGURE=1 ./autogen.sh
}

build() {
  cd $pkgbase
  ./configure \
		--prefix=/usr \
		--sysconfdir=/etc \
		--localstatedir=/var \
		--libexecdir=/usr/lib/dbus-1.0 \
		--with-dbus-user=dbus \
		--with-system-pid-file=/run/dbus/pid \
		--with-system-socket=/run/dbus/system_bus_socket \
		--with-console-auth-dir=/run/console/ \
		--enable-inotify \
		--disable-verbose-mode \
		--disable-static \
		--disable-asserts \
		--disable-systemd \
		--with-systemdsystemunitdir=no 

  make
}

check() {
  cd $pkgbase
  make check
}

package_dbus(){
	
  optdepends=('dbus-s6serv: dbus service for s6'
			'dbus-s6rcserv: dbus service for s6-rc')
	provides=('libdbus')
	conflicts=('libdbus')
	replaces=('libdbus')
  
  cd $pkgbase

  make DESTDIR="$pkgdir" install

  rm -rf "${pkgdir}/var/run"

  #install -Dm755 ../30-dbus "$pkgdir/etc/X11/xinit/xinitrc.d/30-dbus.sh"
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/dbus/COPYING"
  
  # systemd-sysusers
  install -Dm644 "$srcdir/dbus.sysusers" "$pkgdir/usr/lib/sysusers.d/dbus.conf"
  # systemd-tmpfiles
  install -D -m644 "${srcdir}/dbus.tmpfiles" "${pkgdir}/usr/lib/tmpfiles.d/dbus.conf"
  	
  # Split docs
  mv "$pkgdir/usr/share/doc" "$srcdir"	
}

package_dbus-docs(){
  pkgdesc+=" (documentation)"
  depends=('dbus')
  
  install -d "$pkgdir/usr/share/licenses"
  ln -s dbus "$pkgdir/usr/share/licenses/dbus-docs"
  
  mv doc "$pkgdir/usr/share"
}

