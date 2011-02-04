# Maintainer: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=openssh
pkgver=5.8p1
pkgrel=1
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
sha1sums=('adebb2faa9aba2a3a3c8b401b2b19677ab53f0de'
          'ec102deb69cad7d14f406289d2fc11fee6eddbdd'
          '660092c57bde28bed82078f74011f95fc51c2293'
          '6b7f8ebf0c1cc37137a7d9a53447ac8a0ee6a2b5')

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

	# PAM is a common, standard feature to have
	sed -i	-e '/^#ChallengeResponseAuthentication yes$/c ChallengeResponseAuthentication no' \
		-e '/^#UsePAM no$/c UsePAM yes' \
		"$pkgdir"/etc/ssh/sshd_config
}
