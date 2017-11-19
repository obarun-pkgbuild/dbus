# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/dbus
# 						Maintainer: Jan de Groot <jgc@archlinux.org>
# 						Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# 						Contributor: Tom Gundersen <teg@jklm.no>
# 						Contributor: Link Dupont <link@subpop.net>
#

pkgbase=dbus
pkgname=('dbus' 'dbus-docs')
pkgver=1.12.2
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
			'libxcursor' 'libxdamage' 'libfontenc' 'libxfont2'
			'libxft' 'libxi' 'libxinerama' 'libxrandr' 'libxres' 'libxtst'
			'libxv' 'libxvmc' 'libxxf86dga' 'libxxf86vm' 'libdmx' 'libpciaccess'
			'libxkbfile' 'libxshmfence' 'autoconf-archive' 'graphviz')
_commit=0f0968336b9711349023e1d41f075b2bccf7c20b # tags/dbus-1.12.2^0
source=("git+https://anongit.freedesktop.org/git/dbus/dbus.git#commit=$_commit"
        'dbus.sysusers'
        'dbus.tmpfiles')
sha256sums=('SKIP'
            '9bc34a20595df8869d43a8d9af74cbded999c9a004ec12ff8ce07d58d81018d8'
            '965cef20cce35819e89c65f06a931a38bea2119b0ae9c259b5d7f9cfc3edd6d7')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

pkgver() {
  cd $pkgbase
  git describe --tags | sed 's/^dbus-//;s/-/+/g'
}

prepare() {
  cd $pkgbase

  # Reduce docs size
  printf '%s\n' >>Doxyfile.in \
  HAVE_DOT=yes DOT_IMAGE_FORMAT=svg INTERACTIVE_SVG=yes

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

