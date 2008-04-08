# $Id: PKGBUILD,v 1.67 2007/12/03 18:16:04 aaron Exp $
# Maintainer: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=openssh
pkgver=5.0p1
pkgrel=1
#_gsskexver=20080404
pkgdesc='A Secure SHell server/client'
arch=(i686 x86_64)
license=('custom')
url="http://www.openssh.org/portable.html"
backup=('etc/ssh/ssh_config' 'etc/ssh/sshd_config' 'etc/pam.d/sshd')
depends=('openssl>=0.9.8g' 'zlib' 'pam' 'tcp_wrappers' 'heimdal')
source=(ftp://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/$pkgname-$pkgver.tar.gz
        sshd sshd.confd sshd.pam)
        #http://www.sxw.org.uk/computing/patches/$pkgname-$pkgver-gsskex-$_gsskexver.patch
md5sums=('1f1dfaa775f33dd3328169de9bdc292a'
         'd9ee5e0a0d143689b3d6f11454a2a892'
         'e2cea70ac13af7e63d40eb04415eacd5'
         '1c7c2ea8734ec7e3ca58d820634dc73a')

build() {
  cd $startdir/src/$pkgname-$pkgver
  #patch -up0 < $startdir/src/$pkgname-$pkgver-gsskex-$_gsskexver.patch

  #NOTE we disable-strip so that makepkg can decide whether to strip or not
  ./configure --prefix=/usr --libexecdir=/usr/lib/ssh \
    --sysconfdir=/etc/ssh --with-tcp-wrappers --with-privsep-user=nobody \
    --with-md5-passwords --with-pam --with-mantype=man --mandir=/usr/man \
    --with-xauth=/usr/bin/xauth --with-kerberos5=/usr --disable-strip
  make || return 1
  make DESTDIR=$startdir/pkg install

  #What is this for? Is it needed?
  mkdir -p $startdir/pkg/var/empty

  install -D -m755 $startdir/src/sshd $startdir/pkg/etc/rc.d/sshd

  install -D -m644 LICENCE $startdir/pkg/usr/share/licenses/$pkgname/LICENCE
  install -D -m644 $startdir/src/sshd.pam $startdir/pkg/etc/pam.d/sshd
  install -D -m644 $startdir/src/sshd.confd $startdir/pkg/etc/conf.d/sshd

  rm $startdir/pkg/usr/man/man1/slogin.1
  ln -sf ssh.1.gz $startdir/pkg/usr/man/man1/slogin.1.gz

  #additional contrib scripts that we like
  install -D -m755 contrib/findssl.sh $startdir/pkg/usr/bin/findssl.sh
  install -D -m755 contrib/ssh-copy-id $startdir/pkg/usr/bin/ssh-copy-id
  install -D -m644 contrib/ssh-copy-id.1  $startdir/pkg/usr/man/man1/ssh-copy-id.1

  #adjust our config files
  sed -i \
    -e 's|^#ListenAddress 0.0.0.0|ListenAddress 0.0.0.0|g' \
    -e 's|^#UsePAM no|UsePAM yes|g' \
    -e 's|^#ChallengeResponseAuthentication yes|ChallengeResponseAuthentication no|g' \
    $startdir/pkg/etc/ssh/sshd_config
  sed -i -e 's|^# Host \*|Host *|g' $startdir/pkg/etc/ssh/ssh_config
  echo "HashKnownHosts yes" >>  $startdir/pkg/etc/ssh/ssh_config
  echo "StrictHostKeyChecking ask" >>  $startdir/pkg/etc/ssh/ssh_config
}
