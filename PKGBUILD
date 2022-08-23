pkgname=detect-it-easy
pkgver=3.06
pkgrel=1
pkgdesc='Detect It Easy, or abbreviated "DIE" is a program for determining types of files'
arch=('x86_64')
url='https://horsicq.github.io'
license=(MIT)
provides=(
  'detect-it-easy'
)
conflicts=(
  'detect-it-easy'
)
depends=(
  'freetype2'
  'glib2'
  'glibc'
  'graphite'
  'icu'
  'krb5'
  'qt5-base'
  'qt5-script'
  'qt5-svg'
  'systemd-libs'
)
makedepends=(
  'coreutils'
  'git'
  'imagemagick'
  'qt5-tools'
)
_srcname="DIE-engine"
source=(
  'git+https://github.com/horsicq/DIE-engine.git'
)
sha512sums=(
  'SKIP'
)
_pkgname="${pkgname/-git/}"
_stop='\e[m'
_color="\e[33m"
_bold='\e[1m'
_prefix=" ${_bold}${_color}==>${_stop} "

prepare() {
    cd "$srcdir/$_srcname"
    git submodule update --init --recursive
}

build() {
  cd "$_srcname" || return
  echo -e "${_prefix}Building detect-it-easy"

  _subdirs="build_libs gui_source console_source lite_source"

  # [DEPRECATED] FIXME UPSTREAM: -Werror=format-security is causing build errors (merged upstream)
  #export CFLAGS+=" -Wno-format-security"
  #export CXXFLAGS+=" -Wno-format-security"

  for _subdir in $_subdirs; do
    pushd "$_subdir" || return
    echo -e "${_prefix}${_prefix}Building $_subdir"
    qmake-qt5 PREFIX=/usr QMAKE_CFLAGS="${CFLAGS}" QMAKE_CXXFLAGS="${CXXFLAGS}" QMAKE_LFLAGS="${LDFLAGS}" "$_subdir.pro"
    make -f Makefile clean
    make -f Makefile
    popd || return
  done

  echo -e "${_prefix}${_prefix}Running Qt's Linguist tool chain for gui_source"
  cd gui_source || return
  lupdate gui_source_tr.pro
  lrelease gui_source_tr.pro
}

package() {
  cd "$_srcname" || return

  echo -e "${_prefix}Creating the package base"
  install -d "$pkgdir"/{opt/"${_pkgname}",usr/bin,usr/share/pixmaps}
  install -d "$pkgdir/opt/${_pkgname}"/{lang,qss,info,db,signatures,images}

  echo -e "${_prefix}Copying the package binaries"
  install -Dm 755 build/release/die -t "$pkgdir"/opt/"${_pkgname}"
  install -Dm 755 build/release/diec -t "$pkgdir"/opt/"${_pkgname}"
  install -Dm 755 build/release/diel -t "$pkgdir"/opt/"${_pkgname}"

  echo -e "${_prefix}Copying the package files"
  install -Dm 644 gui_source/translation/* -t "$pkgdir"/opt/"${_pkgname}"/lang
  install -Dm 644 XStyles/qss/* -t "$pkgdir"/opt/"${_pkgname}"/qss
  install -Dm 644 Detect-It-Easy/info/* -t "$pkgdir"/opt/"${_pkgname}"/info
  cp -r Detect-It-Easy/db/* -t "$pkgdir"/opt/"${_pkgname}"/db/
  install -Dm 644 signatures/crypto.db -t "$pkgdir"/opt/"${_pkgname}"/signatures
  cp -r images/* -t "$pkgdir"/opt/"${_pkgname}"/images/

  echo -e "${_prefix}Setting up /usr/bin launchers"
  ln -s /opt/"${_pkgname}"/die "$pkgdir"/usr/bin/die
  ln -s /opt/"${_pkgname}"/diec "$pkgdir"/usr/bin/diec
  ln -s /opt/"${_pkgname}"/diel "$pkgdir"/usr/bin/diel

  echo -e "${_prefix}Setting up desktop icon"
  install -Dm 644 LINUX/hicolor/48x48/apps/detect-it-easy.png -t "$pkgdir"/usr/share/pixmaps

  echo -e "${_prefix}Setting up desktop shortcuts"
  install -Dm 644 LINUX/die.desktop -t "$pkgdir"/usr/share/applications
  
  install -Dm 644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}