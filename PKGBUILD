# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/dbus
# 						Maintainer: Jan de Groot <jgc@archlinux.org>
# 						Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# 						Contributor: Tom Gundersen <teg@jklm.no>
# 						Contributor: Link Dupont <link@subpop.net>
#

pkgbase=dbus
pkgname=('dbus' 'dbus-docs')
pkgver=1.10.22
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
_commit=2f8f4d619b16b134671521c2b4aea3a94fb47848 # tags/dbus-1.10.22^0
source=("git+https://anongit.freedesktop.org/git/dbus/dbus#commit=$_commit"
        '30-dbus'
        'dbus.sysusers')
sha256sums=('SKIP'
            'dc1ce6d38674bad7a48ad1911576f8bbb3819f1019126fb1ef7c3cfad16bb02a'
            '1ce179ba3a92ad34941d8ac7f53d01d42cbc91d43ada1136492b78c10b5d693d')
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
			'dbus-s6rcserv: dbus service for s6-rc'
			'dbus-runitserv: dbus service for runit')
	provides=('libdbus')
	conflicts=('libdbus')
	replaces=('libdbus')
  
  cd $pkgbase

  make DESTDIR="$pkgdir" install

  rm -rf "${pkgdir}/var/run"

  install -Dm755 ../30-dbus "$pkgdir/etc/X11/xinit/xinitrc.d/30-dbus.sh"
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/dbus/COPYING"
  
  # systemd-sysusers
  install -Dm644 "$srcdir/dbus.sysusers" "$pkgdir/usr/lib/sysusers.d/dbus.conf"

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

