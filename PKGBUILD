# Maintainer: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=openssh
pkgver=5.6p1
pkgrel=2
pkgdesc='Free version of the SSH connectivity tools'
arch=('i686' 'x86_64')
license=('custom:BSD')
url='http://www.openssh.org/portable.html'
backup=('etc/ssh/ssh_config' 'etc/ssh/sshd_config' 'etc/pam.d/sshd' 'etc/conf.d/sshd')
depends=('tcp_wrappers' 'heimdal' 'libedit')
source=("ftp://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/${pkgname}-${pkgver}.tar.gz"
        'sshd.confd'
        'sshd.pam'
        'sshd')
sha1sums=('347dd39c91c3529f41dae63714d452fb95efea1e'
          'ec102deb69cad7d14f406289d2fc11fee6eddbdd'
          '660092c57bde28bed82078f74011f95fc51c2293'
          '7f56379ad9e6eb197f0db59e7df6473b83c197a1')

build() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	./configure --prefix=/usr --libexecdir=/usr/lib/ssh \
		--sysconfdir=/etc/ssh --with-tcp-wrappers --with-privsep-user=nobody \
		--with-md5-passwords --with-pam --with-mantype=man --mandir=/usr/share/man \
		--with-xauth=/usr/bin/xauth --with-kerberos5=/usr --with-ssl-engine \
		--with-libedit=/usr/lib --disable-strip # stripping is done by makepkg
	make
}

package() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	make DESTDIR="${pkgdir}" install

	install -Dm755 ../sshd "${pkgdir}"/etc/rc.d/sshd
	install -Dm644 ../sshd.pam "${pkgdir}"/etc/pam.d/sshd
	install -Dm644 ../sshd.confd "${pkgdir}"/etc/conf.d/sshd
	install -Dm644 LICENCE "${pkgdir}/usr/share/licenses/${pkgname}/LICENCE"

	rm "${pkgdir}"/usr/share/man/man1/slogin.1
	ln -sf ssh.1.gz "${pkgdir}"/usr/share/man/man1/slogin.1.gz

	# additional contrib scripts that we like
	install -Dm755 contrib/findssl.sh "${pkgdir}"/usr/bin/findssl.sh
	install -Dm755 contrib/ssh-copy-id "${pkgdir}"/usr/bin/ssh-copy-id
	install -Dm644 contrib/ssh-copy-id.1 "${pkgdir}"/usr/share/man/man1/ssh-copy-id.1
}
