# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Giancarlo Razzolini <grazzolini@archlinux.org>
# Contributor: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>
# Changes by Matthijs Tadema

pkgname=openssh-custom-git
pkgver=r11916.736e1c5b
pkgrel=1
pkgdesc="SSH protocol implementation for remote login, command execution and file transfer"
arch=('x86_64')
url='https://www.openssh.com/portable.html'
license=('custom:BSD')
provides=("${pkgname%-custom-git}" "${pkgname%-custom-git}-debug")
conflicts=("${pkgname%-custom-git}" "${pkgname%-custom-git}-debug")
depends=(
  'glibc'
  'krb5' 'libkrb5.so' 'libgssapi_krb5.so'
  'ldns'
  'libedit'
  'libxcrypt' 'libcrypt.so'
  'openssl'
  'pam' 'libpam.so'
  'zlib'
)
makedepends=('libfido2' 'linux-headers')
optdepends=(
  'libfido2: FIDO/U2F support'
  'x11-ssh-askpass: input passphrase in X'
  'xorg-xauth: X11 forwarding'
)
backup=(
  'etc/pam.d/sshd'
  'etc/ssh/ssh_config'
  'etc/ssh/sshd_config'
)
options=('debug')
source=(
  "${pkgname%-custom-git}::git+https://github.com/mjtadema/openssh-portable.git#branch=master"
  'sshdgenkeys.service'
  'sshd.service'
  'sshd.conf'
  'sshd.pam'
)
sha256sums=('SKIP'
            'e5305767b2d317183ad1c5022a5f6705bd9014a8b22495a000fd482713738611'
            'e40f8b7c8e5e2ecf3084b3511a6c36d5b5c9f9e61f2bb13e3726c71dc7d4fbc7'
            '4effac1186cc62617f44385415103021f72f674f8b8e26447fc1139c670090f6'
            '64576021515c0a98b0aaf0a0ae02e0f5ebe8ee525b1e647ab68f369f81ecd846')
b2sums=('SKIP'
        '09fad3648f48f13ee80195b90913feeba21240d121b1178e0ce62f4a17b1f7e58e8edc22c04403e377ab300f5022a804c848f5be132765d5ca26a38aab262e50'
        '07ad5c7fb557411a6646ff6830bc9d564c07cbddc4ce819641d31c05dbdf677bfd8a99907cf529a7ee383b8c250936a6423f4b4b97ba0f1c14f627bbd629bd4e'
        '27571f728c3c10834a81652f3917188436474b588f8b047462e44b6c7a424f60d06ce8cb74839b691870177d7261592207d7f35d4ae6c79af87d6a7ea156d395'
        '557d015bca7008ce824111f235da67b7e0051a693aaab666e97b78e753ed7928b72274af03d7fde12033986b733d5f996faf2a4feb6ecf53f39accae31334930')
validpgpkeys=('7168B983815A5EEF59A4ADFD2A3F414E736060BA')  # Damien Miller <djm@mindrot.org>

pkgver() {
    cd "$srcdir/${pkgname%-custom-git}"
# Git, no tags available
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

#prepare() {
# I guess has been fixed upstream
#  patch -Np1 -d "${pkgname%-custom-git}-$pkgver" -i ../${pkgname%-custom-git}-9.0p1-sshd_config.patch
#}

build() {
  cd "$srcdir/${pkgname%-custom-git}"

  autoreconf
  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --libexecdir=/usr/lib/ssh \
    --sysconfdir=/etc/ssh \
    --disable-strip \
    --with-ldns \
    --with-libedit \
    --with-security-key-builtin \
    --with-ssl-engine \
    --with-pam \
    --with-privsep-user=nobody \
    --with-kerberos5=/usr \
    --with-xauth=/usr/bin/xauth \
    --with-pid-dir=/run \
    --with-default-path='/usr/local/sbin:/usr/local/bin:/usr/bin' \

  make
}

check() {
  cd "$srcdir/${pkgname%-custom-git}"

  # NOTE: make t-exec does not work in our build environment
  make file-tests interop-tests unit
}

package() {
  cd "$srcdir/${pkgname%-custom-git}"

  make DESTDIR="${pkgdir}" install

  ln -sf ssh.1.gz "${pkgdir}"/usr/share/man/man1/slogin.1.gz
  install -Dm644 LICENCE -t "${pkgdir}/usr/share/licenses/${pkgname%-custom-git}/"

  install -Dm644 ../sshdgenkeys.service -t "${pkgdir}"/usr/lib/systemd/system/
  install -Dm644 ../sshd.service -t "${pkgdir}"/usr/lib/systemd/system/
  install -Dm644 ../sshd.conf -t "${pkgdir}"/usr/lib/tmpfiles.d/
  install -Dm644 ../sshd.pam "${pkgdir}"/etc/pam.d/sshd

  install -Dm755 contrib/findssl.sh -t "${pkgdir}"/usr/bin/
  install -Dm755 contrib/ssh-copy-id -t "${pkgdir}"/usr/bin/
  install -Dm644 contrib/ssh-copy-id.1 -t "${pkgdir}"/usr/share/man/man1/
}

# vim: ts=2 sw=2 et:
