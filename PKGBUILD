# Maintainer: Vasily Kakashkin <f.f.miak at mail.ru>

pkgname=remod-svn
pkgver=188
pkgrel=1
pkgdesc="A SVN version of modified Sauerbraten server."
arch=('i686' 'x86_64')
url="http://code.google.com/p/remod-sauerbraten/"
license=('ZLIB')
depends=()
makedepends=('subversion')
install=remod.install
source=(remod.confd remod.rcd remod.install)
md5sums=('602c74c72beb43e53f60d7ccc62567d9'
         'd406394a76b687f66554329b9a1e76f0'
         '3211c9a11d77873d76e8fefab6d16900')


_svntrunk=http://remod-sauerbraten.googlecode.com/svn/trunk/
_svnmod=remod-svn

build() {
  cd "$srcdir"
  msg "Connecting to SVN server...."

  if [[ -d "$_svnmod/.svn" ]]; then
    (cd "$_svnmod" && svn up -r "$pkgver")
  else
    svn co "$_svntrunk" --config-dir ./ -r "$pkgver" "$_svnmod"
  fi

  msg "SVN checkout done or server timeout"
  msg "Starting build..."

  rm -rf "$srcdir/$_svnmod-build"
  cp -r "$srcdir/$_svnmod" "$srcdir/$_svnmod-build"
  cd "$srcdir/$_svnmod-build"

  #
  # BUILD HERE
  #
  make
}

package() {

  #system platform
  sysarch="`uname -m`"
  case ${sysarch} in
    x86_64|amd64)
    servername=remod64
  ;;
  *)
    servername=remod 
  ;;
  esac

  cd "$srcdir/$_svnmod-build"

  #make dirs
  mkdir -p ${pkgdir}/usr/share/remod/{docs,scripts}
  mkdir -p ${pkgdir}/usr/share/remod/docs/database_init
  mkdir -p ${pkgdir}/usr/share/remod/scripts/{commands,irccommands,other}
  mkdir -p ${pkgdir}/var/remod/{maps,demos}

  #bin, same on all platforms
  install -Dm755 ${servername} ${pkgdir}/usr/bin/remod

  #rc.d
  install -Dm644 ${srcdir}/remod.confd ${pkgdir}/etc/conf.d/remod
  install -Dm755 ${srcdir}/remod.rcd ${pkgdir}/etc/rc.d/remod

  #scripts
  install -Dm644 scripts/*.cfg ${pkgdir}/usr/share/remod/scripts
  install -Dm644 scripts/commands/*.cfg ${pkgdir}/usr/share/remod/scripts/commands
  install -Dm644 scripts/irccommands/*.cfg ${pkgdir}/usr/share/remod/scripts/irccommands
  install -Dm644 scripts/other/*.cfg ${pkgdir}/usr/share/remod/scripts/other

  #docs
  install -Dm644 docs/*.html ${pkgdir}/usr/share/remod/docs
  install -Dm644 docs/database_init/* ${pkgdir}/usr/share/remod/docs/database_init

  #home
  install -Ddm755 demos ${pkgdir}/var/remod
  install -Ddm755 maps ${pkgdir}/var/remod
  install -Dm755 GeoIP.dat ${pkgdir}/var/remod/GeoIP.dat
  install -Dm755 remod_sqlite3.db ${pkgdir}/var/remod/remod_sqlite3.db
  install -Dm755 server-init.cfg ${pkgdir}/var/remod/server-init.cfg.example
  install -Dm755 permbans.cfg ${pkgdir}/var/remod/permbans.cfg.example
}
