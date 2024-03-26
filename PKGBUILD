# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Giancarlo Razzolini <grazzolini@archlinux.org>
# Contributor: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>
# Modifications: matthijs
# 
# First rebase '--onto' any changes from upstream pkgbuild
# Bump version if necessary

pkgname=openssh-custom
pkgver=9.7p1
pkgrel=1
pkgdesc="SSH protocol implementation for remote login, command execution and file transfer"
arch=(x86_64)
url='https://www.openssh.com/portable.html'
license=(
  BSD-2-Clause
  BSD-3-Clause
  ISC
  MIT
)
provides=(openssh)
conflicts=(openssh)
depends=(
  glibc
  krb5 libkrb5.so libgssapi_krb5.so
  ldns
  libedit
  libxcrypt libcrypt.so
  openssl
  pam libpam.so
  zlib
)
makedepends=(
  libfido2
  linux-amd-raven-headers
  autoconf
)
optdepends=(
  'libfido2: FIDO/U2F support'
  'sh: for ssh-copy-id and findssl.sh'
  'x11-ssh-askpass: input passphrase in X'
  'xorg-xauth: X11 forwarding'
)
backup=(
  etc/pam.d/sshd
  etc/ssh/ssh_config
  etc/ssh/sshd_config
)
_TAG=`sed 's/\./_/;s/p/_P/' <<< "V_${pkgver}"`
source=(
  "$pkgname::git+https://github.com/openssh/openssh-portable.git#tag=${_TAG}"
  99-archlinux.conf
  sshdgenkeys.service
  sshd.service
  ssh-agent.service
  sshd.conf
  sshd.pam
  forward.patch
)
sha256sums=('SKIP'
            '78b806c38bc1e246daaa941bfe7880e6eb6f53f093bea5d5868525ae6d223d30'
            'e5305767b2d317183ad1c5022a5f6705bd9014a8b22495a000fd482713738611'
            'e40f8b7c8e5e2ecf3084b3511a6c36d5b5c9f9e61f2bb13e3726c71dc7d4fbc7'
            'b3b1e4f7af169cd5fccdcdf9538ef37fc919c79a9905f797925153a94e723998'
            '76635a91526ce44571485e292e3a777ded6a439af78cb93514b999f91fb9b327'
            '633e24cbfcb045ba777d3e06d5f85dfaa06d44f4727d38c7fb2187c57498221d'
            'c1a8a944d959843bfbbde2331ee806a84c166bffdc90c92318d40ff91cf9c46a')
b2sums=('SKIP'
        '1ff8cd4ae22efed2b4260f1e518de919c4b290be4e0b5edbc8e2225ffe63788678d1961e6f863b85974c4697428ee827bcbabad371cfc91cc8b36eae9402eb97'
        '09fad3648f48f13ee80195b90913feeba21240d121b1178e0ce62f4a17b1f7e58e8edc22c04403e377ab300f5022a804c848f5be132765d5ca26a38aab262e50'
        '07ad5c7fb557411a6646ff6830bc9d564c07cbddc4ce819641d31c05dbdf677bfd8a99907cf529a7ee383b8c250936a6423f4b4b97ba0f1c14f627bbd629bd4e'
        '046ea6bd6aa00440991e5f7998db33864a7baa353ec6071f96a3ccb5cca5b548cb9e75f9dee56022ca39daa977d18452851d91e6ba36a66028b84b375ded9bc5'
        'a3fd8f00430168f03dcbc4a5768ed788dd43140e365a882b601510f53f69704da04f24660157bb8a43125f5389528993732d99569d77d5f3358074e7ae36d4ca'
        '1d24cc029eccf71cee54dda84371cf9aa8d805433e751575ab237df654055dd869024b50facd8b73390717e63100c76bca28b493e0c8be9791c76a2e0d60990a'
        'a7f225bb918d7d465eb3d89cdb48ec40ebfba52cac491599b83882a21250bab7d6ab38bd0eae5b970a7cc0b94c3c0e4994203cb6e39ab9596b1e1b0273c2ed3a')
validpgpkeys=('7168B983815A5EEF59A4ADFD2A3F414E736060BA')  # Damien Miller <djm@mindrot.org>

prepare() {
  cd $srcdir/$pkgname
  # remove variable (but useless) first line in config (related to upstream VCS)
  sed '/^#.*\$.*\$$/d' -i ssh{,d}_config

  # prepend configuration option to include drop-in configuration files for sshd_config
  printf "# Include drop-in configurations\nInclude /etc/ssh/sshd_config.d/*.conf\n" | cat - sshd_config > sshd_config.tmp
  mv -v sshd_config.tmp sshd_config
  # prepend configuration option to include drop-in configuration files for ssh_config
  printf "# Include drop-in configurations\nInclude /etc/ssh/ssh_config.d/*.conf\n" | cat - ssh_config > ssh_config.tmp
  mv -v ssh_config.tmp ssh_config

  # Apply forward expansion patch
  patch -p1 -i "$srcdir/forward.patch"
}

build() {
  cd $srcdir/$pkgname
  local configure_options=(
    --prefix=/usr
    --sbindir=/usr/bin
    --libexecdir=/usr/lib/ssh
    --sysconfdir=/etc/ssh
    --disable-strip
    --with-ldns
    --with-libedit
    --with-security-key-builtin
    --with-ssl-engine
    --with-pam
    --with-privsep-user=nobody
    --with-kerberos5=/usr
    --with-xauth=/usr/bin/xauth
    --with-pid-dir=/run
    --with-default-path='/usr/local/sbin:/usr/local/bin:/usr/bin'
    --without-zlib-version-check
  )
  
  autoreconf
  ./configure "${configure_options[@]}"
  make
}

check() {
  cd $srcdir/$pkgname
  # NOTE: make t-exec does not work in our build environment
  make file-tests interop-tests unit
}

package() {
  cd $srcdir/$pkgname

  make DESTDIR="$pkgdir" install

  install -vDm 644 ../99-archlinux.conf -t "$pkgdir/etc/ssh/sshd_config.d/"
  install -vdm 755 "$pkgdir/etc/ssh/ssh_config.d"

  ln -sf ssh.1.gz "$pkgdir"/usr/share/man/man1/slogin.1.gz
  install -Dm644 LICENCE -t "$pkgdir/usr/share/licenses/$pkgname/"

  install -Dm644 ../sshdgenkeys.service -t "$pkgdir"/usr/lib/systemd/system/
  install -Dm644 ../sshd.service -t "$pkgdir"/usr/lib/systemd/system/
  install -Dm644 ../ssh-agent.service -t "$pkgdir"/usr/lib/systemd/user/
  install -Dm644 ../sshd.conf -t "$pkgdir"/usr/lib/tmpfiles.d/
  install -Dm644 ../sshd.pam "$pkgdir"/etc/pam.d/sshd

  install -Dm755 contrib/findssl.sh -t "$pkgdir"/usr/bin/
  install -Dm755 contrib/ssh-copy-id -t "$pkgdir"/usr/bin/
  install -Dm644 contrib/ssh-copy-id.1 -t "$pkgdir"/usr/share/man/man1/
}

# vim: ts=2 sw=2 et:
