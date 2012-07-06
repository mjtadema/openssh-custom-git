# Maintainer: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=openssh
pkgver=6.0p1
pkgrel=3
pkgdesc='Free version of the SSH connectivity tools'
url='http://www.openssh.org/portable.html'
license=('custom:BSD')
arch=('i686' 'x86_64')
depends=('krb5' 'openssl' 'libedit' 'ldns')
optdepends=('xorg-xauth: X11 forwarding'
            'x11-ssh-askpass: input passphrase in X')
source=("ftp://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/${pkgname}-${pkgver}.tar.gz"
        'sshd.close-sessions'
        'sshdgenkeys.service'
        'sshd@.service'
        'sshd.service'
        'sshd.socket'
        'sshd.confd'
        'sshd.pam'
        'sshd')
sha1sums=('f691e53ef83417031a2854b8b1b661c9c08e4422'
          '954bf1660aa32620c37034320877f4511b767ccb'
          '6c71de2c2ca9622aa8e863acd94b135555e11125'
          'bd6eae36c7ef9efb7147778baad7858b81f2d660'
          'f27617eeb694f4edd474638adf16733d8a793d85'
          'a30fb5fda6d0143345bae47684edaffb8d0a92a7'
          'ec102deb69cad7d14f406289d2fc11fee6eddbdd'
          '659e3ee95c269014783ff8b318c6f50bf7496fbd'
          '1488d4ed33cf3037accf4b0e1c7a7e90b6a097c7')

backup=('etc/ssh/ssh_config' 'etc/ssh/sshd_config' 'etc/pam.d/sshd' 'etc/conf.d/sshd')

build() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	./configure \
		--prefix=/usr \
		--libexecdir=/usr/lib/ssh \
		--sysconfdir=/etc/ssh \
		--with-ldns \
		--with-libedit \
		--with-ssl-engine \
		--with-pam \
		--with-privsep-user=nobody \
		--with-kerberos5=/usr \
		--with-xauth=/usr/bin/xauth \
		--with-mantype=man \
		--with-md5-passwords \
		--with-pid-dir=/run \

	make
}

check() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	make tests ||
	grep $USER /etc/passwd | grep -q /bin/false
	# connect.sh fails when run with stupid login shell
}

package() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	make DESTDIR="${pkgdir}" install

	rm "${pkgdir}"/usr/share/man/man1/slogin.1
	ln -sf ssh.1.gz "${pkgdir}"/usr/share/man/man1/slogin.1.gz

	install -Dm644 LICENCE "${pkgdir}/usr/share/licenses/${pkgname}/LICENCE"

	install -Dm644 ../sshdgenkeys.service "${pkgdir}"/usr/lib/systemd/system/sshdgenkeys.service
	install -Dm644 ../sshd@.service "${pkgdir}"/usr/lib/systemd/system/sshd@.service
	install -Dm644 ../sshd.service "${pkgdir}"/usr/lib/systemd/system/sshd.service
	install -Dm644 ../sshd.socket "${pkgdir}"/usr/lib/systemd/system/sshd.socket

	install -Dm755 ../sshd.close-sessions "${pkgdir}/etc/rc.d/functions.d/sshd-close-sessions" # FS#17389
	install -Dm644 ../sshd.confd "${pkgdir}"/etc/conf.d/sshd
	install -Dm644 ../sshd.pam "${pkgdir}"/etc/pam.d/sshd
	install -Dm755 ../sshd "${pkgdir}"/etc/rc.d/sshd

	install -Dm755 contrib/findssl.sh "${pkgdir}"/usr/bin/findssl.sh
	install -Dm755 contrib/ssh-copy-id "${pkgdir}"/usr/bin/ssh-copy-id
	install -Dm644 contrib/ssh-copy-id.1 "${pkgdir}"/usr/share/man/man1/ssh-copy-id.1

	sed \
		-e '/^#ChallengeResponseAuthentication yes$/c ChallengeResponseAuthentication no' \
		-e '/^#UsePAM no$/c UsePAM yes' \
		-i "${pkgdir}"/etc/ssh/sshd_config
}
