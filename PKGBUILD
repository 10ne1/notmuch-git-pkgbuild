# Maintainer: Stefan Husmann <stefan-husmann@t-online.de>
# Contributor: Lukas Fleischer <lfleischer@archlinux.org>
# Contributor: Daniel Wallace <danielwallace at gtmanfred dot com>
# Contributor: fauno <fauno at kiwwwi.com.ar>
# Contributor: Olivier Ramonat <olivier at ramonat dot fr>
# Contributor: Richard Murri <admin@richardmurri.com>

pkgbase=notmuch-git
arch=('i686' 'x86_64' 'armv7h')
pkgname=('notmuch-runtime-git')
epoch=3
pkgver=0.27.3.31.g8dcc38ce
pkgrel=1
url="https://notmuchmail.org/"
license=('GPL3')
makedepends=('python' 'git' 'gnupg' 'pkgconfig' 'xapian-core' 'gmime' 'talloc')
options=(!distcc !makeflags)
source=("git+https://github.com/10ne1/notmuch#branch=dev/aratiu/filesize-query-master-rebase")
md5sums=('SKIP')

pkgver() {
  cd "${pkgbase%-git}"
  git describe --long --tags|cut -d/ -f 3 |tr - .
}

prepare(){
  cd "${pkgbase%-git}"
  cp -dpr --no-preserve=ownership "$srcdir"/${pkgbase%-git}/bindings/python{,2}
  find "$srcdir/${pkgbase%-git}/bindings/python" -name '*.py' -exec sed -i -e '1s,python$,python3,' {} +
  find "$srcdir/${pkgbase%-git}/bindings/python2" -name '*.py' -exec sed -i -e '1s,python$,python2,' {} +
}

build() {
  cd "${pkgbase%-git}"
  ./configure --prefix=/usr --sysconfdir=/etc --includedir=/usr/include --without-zsh-completion --without-ruby --without-emacs --without-api-docs --without-docs --without-desktop
  make -j6
}

package_notmuch-emacs-git(){
  arch=('any')
  pkgdesc="Not much of an email program -default interface to emacs"
  depends=('notmuch-runtime')
  optdepends=('emacs: for using the emacs interface' 'gnupg: for email encryption')
  conflicts=('notmuch')
  provides=('notmuch')
  install=notmuch.install
 
  cd "${pkgbase%-git}"
  make DESTDIR="$pkgdir/" install-emacs
  rm "$pkgdir/usr/share/applications/mimeinfo.cache"
}

package_notmuch-runtime-git(){
  arch=('i686' 'x86_64' 'armv7h')
  pkgdesc="Runtime for notmuch and notmuch-mutt"
  depends=('xapian-core' 'gmime' 'talloc')
  conflicts=('notmuch-runtime')
  provides=('notmuch-runtime')
  cd "${pkgbase%-git}"
  
  make DESTDIR="$pkgdir/" LIBDIR_IN_LDCONFIG=0 WITH_EMACS=0 install
  
  # install manpages
  make DESTDIR="$pkgdir" prefix=/usr install-man
  
  # install binary
  install -Dm755 notmuch "$pkgdir/usr/bin/notmuch"

  # remove stuff that goes into the other packages
  rm -rf "$pkgdir/usr/share/emacs"
}

package_notmuch-vim-git(){
  arch=('any')
  pkgdesc="Vim plugins for notmuch"
  depends=('notmuch-runtime')
  optdepends=('vim: for using the vim interface')
  conflicts=('notmuch-vim')
  provides=('notmuch-vim')
  
  make -C "${pkgbase%-git}"/vim DESTDIR="$pkgdir" prefix="/usr/share/vim/vimfiles" install
}

package_notmuch-python-git(){
  arch=('any')
  depends=('notmuch-runtime')
  pkgdesc="Python bindings for notmuch"
  optdepends=('python: for using the python bindings')
  conflicts=('notmuch-python')
  provides=('notmuch-python') 

  cd "${pkgbase%-git}"/bindings/python
  python setup.py install --prefix=/usr --root="$pkgdir"
}

package_notmuch-python2-git(){
  arch=('any')
  depends=('notmuch-runtime')
  pkgdesc="Python2 bindings for notmuch"
  optdepends=('python2: for using the python2 bindings')
  conflicts=('notmuch-python2')
  provides=('notmuch-python2')
  cd "${pkgbase%-git}"/bindings/python2
  python2 setup.py install --prefix=/usr --root="$pkgdir"
}

package_notmuch-ruby-git(){
  arch=('i686' 'x86_64' 'armv7h')
  depends=('notmuch-runtime')
  pkgdesc="Ruby bindings for notmuch"
  optdepends=('ruby: for using the ruby bindings')
  conflicts=('notmuch-ruby')
  provides=('notmuch-ruby')
  
  cd ${pkgbase%-git}
  sed -i 's:INSTALL = .*[^D]$:& -D:' bindings/ruby/Makefile
  install -d "$pkgdir"/usr/lib/ruby/vendor_ruby/2.4.0/x86_64-linux
  make -C bindings/ruby exec_prefix="$pkgdir"/usr prefix=/usr install  
}

package_notmuch-mutt-git(){
  arch=('any')
  pkgdesc="The mail indexer"
  depends=('notmuch-runtime' 'perl-mailtools' 'perl-mail-box' 'perl-term-readline-gnu' 'perl-string-shellquote' 'perl-file-which')
  conflicts=('notmuch-mutt')
  provides=('notmuch-mutt') 
    
  cd ${pkgbase%-git}
  
  install -Dm755 "contrib/${pkgbase%-git}-mutt/${pkgbase%-git}-mutt" "$pkgdir"/usr/bin/${pkgbase%-git}-mutt
  install -Dm644 "contrib/${pkgbase%-git}-mutt/${pkgbase%-git}-mutt.1" "${pkgdir}"/usr/share/man/man1/${pkgbase%-git}-mutt.1
}
