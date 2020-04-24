# Maintainer: Pierre Schmitz <pierre@archlinux.de>
# Contributor: Nattapong Amornbunchornvej <nattapong@chochoe.net>

pkgname=openssl-static
_pkgname=openssl
_ver=1.1.1g
# use a pacman compatible version scheme
pkgver=${_ver/[a-z]/.${_ver//[0-9.]/}}
pkgrel=2
pkgdesc='The Open Source toolkit for Secure Sockets Layer and Transport Layer Security'
arch=('x86_64')
url='https://www.openssl.org'
license=('custom:BSD')
depends=('perl')
optdepends=('ca-certificates')
options=('staticlibs')
source=("https://www.openssl.org/source/${_pkgname}-${_ver}.tar.gz"{,.asc}
        'ca-dir.patch')
sha256sums=('ddb04774f1e32f0c49751e21b67216ac87852ceb056b75209af2443400636d46'
            'SKIP'
            '0938c8d68110768db4f350a7ec641070686904f2fe7ba630ac94399d7dc8cc5e')
validpgpkeys=('8657ABB260F056B1E5190839D9C4D26D0E604491'
              '7953AC1FBC3DC8B3B292393ED5E9E43F7DF9EE8C')

prepare() {
	cd "$srcdir/$_pkgname-$_ver"

	# set ca dir to /etc/ssl by default
	patch -p0 -i "$srcdir/ca-dir.patch"
}

build() {
	cd "$srcdir/$_pkgname-$_ver"

	# mark stack as non-executable: http://bugs.archlinux.org/task/12434
	./Configure --prefix=/usr --openssldir=/etc/ssl --libdir=lib \
		no-shared no-ssl3-method enable-ec_nistp_64_gcc_128 linux-x86_64 \
		"-Wa,--noexecstack ${CPPFLAGS} ${CFLAGS} ${LDFLAGS}"

	make depend
	make
}

check() {
	cd "$srcdir/$_pkgname-$_ver"
	# the test fails due to missing write permissions in /etc/ssl
	# revert this patch for make test
	patch -p0 -R -i "$srcdir/ca-dir.patch"
	make test
	patch -p0 -i "$srcdir/ca-dir.patch"
}

package() {
	cd "$srcdir/$_pkgname-$_ver"
	make DESTDIR=$pkgdir MANDIR=/usr/share/man MANSUFFIX=ssl install_sw install_ssldirs install_man_docs
    rm -rf "$pkgdir/usr/"{bin,include,share,lib/engines,lib/pkgconfig} $pkgdir/usr/lib/*.so* $pkgdir/etc
	install -D -m644 LICENSE $pkgdir/usr/share/licenses/$pkgname/LICENSE
}
