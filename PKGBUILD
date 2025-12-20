# Maintainer: ZeStig <o0vckutt@duck.com>
# based on https://gitlab.archlinux.org/archlinux/packaging/packages/emacs/-/blob/main/PKGBUILD?ref_type=heads
pkgbase=emacs
pkgname=(emacs-pgtk emacs-lucid emacs-pgtk-nativecomp emacs-lucid-nativecomp)
pkgver=30.2
pkgrel=1
pkgdesc="Wayland-native Emacs build for StratOS"
arch=('x86_64')
url='https://github.com/stratos-linux/stratmacs'
license=('GPL-3.0-or-later')
# options=(!strip)
optdepends=(
    'git: Magit'
    # 'cmake'
    'vterm: Terminal emulator'
    'pandoc-bin: nigh-universal document conversion tool'
    'mu: Email client'
    'notmuch: Email client'
    'ttf-jetbrains-mono-nerd: Default nerd font'
)
makedepends=(git cmake libgccjit)
depends=(
  gmp
  gnutls
  lcms2
  libacl.so
  libasound.so
  libgccjit
  libdbus-1.so
  libfontconfig.so
  libfreetype.so
  libgdk-3.so
  libgdk_pixbuf-2.0.so
  libgif.so
  libgio-2.0.so
  libglib-2.0.so
  libgobject-2.0.so
  libgpm.so
  libgtk-3.so
  libharfbuzz.so
  libice
  libjpeg.so
  libncursesw.so
  libotf
  libpango-1.0.so
  libpng
  librsvg-2.so
  libsm
  sqlite libsqlite3.so
  libsystemd.so
  libtiff.so
  libtree-sitter.so
  libvterm
  libwebp.so
  libwebpdemux.so
  libxfixes
  libxml2.so
  m17n-lib
  zlib
)


source=(
  https://ftp.gnu.org/gnu/emacs/emacs-${pkgver}.tar.xz{,.sig}
)
b2sums=('9163ba6bfab1010a156c669ac085ad363545d73e3ffac21c710b14b618df61a4c6a80a50fd3fa81d852c2ccace5080e614b679606fa584e28509f99ad6196784'
        'SKIP')
# validpgpkeys=('17E90D521672C04631B1183EE78DAE0F3115E06B') # Eli Zaretskii <eliz@gnu.org>
prepare() {
  cp -a emacs-${pkgver} ${srcdir}/emacs-${pkgver}-pgtk
  cp -a emacs-${pkgver} ${srcdir}/emacs-${pkgver}-lucid
  cp -a emacs-${pkgver} ${srcdir}/emacs-${pkgver}-pgtk-nativecomp
  cp -a emacs-${pkgver} ${srcdir}/emacs-${pkgver}-lucid-nativecomp
}

_build_emacs_variant() {
  local variant=$1
  shift
  local flags=("$@")

  cd "${srcdir}/emacs-${pkgver}-${variant}"
  export CFLAGS+=" -O2"
  export ac_cv_lib_gif_EGifPutExtensionLast=yes
  ./configure \
    --prefix=/usr \
    --sysconfdir=/etc \
    --libexecdir=/usr/lib \
    --localstatedir=/var \
    --with-gnutls \
    --with-tree-sitter \
    --with-png \
    --with-jpeg \
    --without-wide-int \
    --with-sound \
    --with-sqlite3 \
    --with-xpm=ifavailable \
    --with-gif=ifavailable \
    --with-libsystemd \
    --with-dbus \
    --with-pdumper \
    --without-pop \
    --without-mailutils \
    --disable-gc-mark-trace \
    "${flags[@]}"
  make -j
}

build() {
  _build_emacs_variant pgtk --with-pgtk --without-native-compilation --without-xwidgets
  _build_emacs_variant lucid --with-x-toolkit=lucid --without-native-compilation # --with-xwidgets
  _build_emacs_variant pgtk-nativecomp --with-pgtk --with-native-compilation --without-xwidgets
  _build_emacs_variant lucid-nativecomp --with-x-toolkit=lucid --with-native-compilation # --with-xwidgets
}

_package_emacs_variant() {
  local variant=$1
  local pkgdesc="$2"

  cd "${srcdir}/emacs-${pkgver}-${variant}"
  make DESTDIR="${pkgdir}" install

  # Avoid conflict with ctags package
  # mv "${pkgdir}/usr/bin/ctags" "${pkgdir}/usr/bin/ctags.emacs"
  # mv "${pkgdir}/usr/share/man/man1/ctags.1.gz" "${pkgdir}/usr/share/man/man1/ctags.emacs.1"

  # chown -R root:root "${pkgdir}/usr/share/emacs/${pkgver}"
}

package_emacs-pgtk() {
  pkgdesc='Emacs with PGTK (Pure GTK) support for Wayland'
  provides=(emacs)
  conflicts=(emacs)
  _package_emacs_variant pgtk "$pkgdesc"
}

package_emacs-lucid() {
  pkgdesc='Emacs with the classic X11 Lucid toolkit'
  provides=(emacs)
  conflicts=(emacs)
  _package_emacs_variant lucid "$pkgdesc"
}

package_emacs-pgtk-nativecomp() {
  pkgdesc='Emacs with PGTK and native compilation enabled'
  depends+=(libgccjit)
  provides=(emacs)
  conflicts=(emacs)
  _package_emacs_variant pgtk-nativecomp "$pkgdesc"
}

package_emacs-lucid-nativecomp() {
  pkgdesc='Emacs with Lucid X11 toolkit and native compilation'
  depends+=(libgccjit)
  provides=(emacs)
  conflicts=(emacs)
  _package_emacs_variant lucid-nativecomp "$pkgdesc"
}
