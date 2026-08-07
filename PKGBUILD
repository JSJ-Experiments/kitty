# Maintainer: JadenJSJ <jadenjsj@proton.me>
# Based on the official Arch Linux kitty PKGBUILD:
# https://gitlab.archlinux.org/archlinux/packaging/packages/kitty
# Builds from the checked-out JSJ-Experiments/kitty tree (bold_is_bright patch applied by CI)
# Version tracks the Arch Linux extra/kitty package.

pkgbase=kitty
pkgname=(kitty kitty-terminfo kitty-shell-integration)
pkgver=0.48.2
pkgrel=1
pkgdesc="A modern, hackable, featureful, OpenGL-based terminal emulator (with bold_is_bright patch)"
arch=('x86_64')
url="https://github.com/JSJ-Experiments/kitty"
license=('GPL-3.0-only')
depends=(
    'cairo'
    'dbus'
    'freetype2'
    'fontconfig'
    'harfbuzz'
    'hicolor-icon-theme'
    'lcms2'
    'libgl'
    'libpng'
    'librsync'
    'libx11'
    'libxcursor'
    'libxkbcommon'
    'libxkbcommon-x11'
    'libxi'
    'openssl'
    'python3'
    'wayland'
    'xxhash'
    'zlib'
)
makedepends=(
    'go'
    'libxinerama'
    'libxrandr'
    'simde'
    'python-sphinx'
    'python-sphinx-copybutton'
    'python-sphinx-inline-tabs'
    'python-sphinxext-opengraph'
    'python-sphinx-furo'
    'ttf-nerd-fonts-symbols-mono'
    'wayland-protocols'
)
options=("!lto" "!debug")
# local tarball created by CI next to this PKGBUILD from the patched checkout
source=("$pkgbase-$pkgver.tar.gz")
sha256sums=('SKIP')

build() {
    cd "$pkgbase-$pkgver"

    export CGO_CPPFLAGS="${CPPFLAGS}"
    export CGO_CFLAGS="${CFLAGS}"
    export CGO_CXXFLAGS="${CXXFLAGS}"
    export CGO_LDFLAGS="${LDFLAGS}"
    export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external -mod=readonly -modcacherw"
    python3 setup.py linux-package --update-check-interval=0
}

package_kitty() {
    depends+=('kitty-terminfo' 'kitty-shell-integration')
    optdepends=('imagemagick: viewing images with icat'
                'python-pygments: syntax highlighting in kitty +kitten diff'
                'libcanberra: playing "bell" sound on terminal bell')

    cd "$srcdir/$pkgname-$pkgver"

    cp -r linux-package "${pkgdir}"/usr

    # completions
    linux-package/bin/kitten __complete__ setup bash | install -Dm644 /dev/stdin "${pkgdir}"/usr/share/bash-completion/completions/kitty
    linux-package/bin/kitten __complete__ setup fish | install -Dm644 /dev/stdin "${pkgdir}"/usr/share/fish/vendor_completions.d/kitty.fish
    linux-package/bin/kitten __complete__ setup zsh  | install -Dm644 /dev/stdin "${pkgdir}"/usr/share/zsh/site-functions/_kitty

    install -Dm 644 "${pkgdir}"/usr/share/icons/hicolor/256x256/apps/kitty.png "${pkgdir}"/usr/share/pixmaps/kitty.png

    rm -r "$pkgdir"/usr/share/terminfo
    rm -r "$pkgdir"/usr/lib/kitty/shell-integration

    install -Dm 644 docs/_build/html/_downloads/*/kitty.conf "${pkgdir}"/usr/share/doc/${pkgname}/kitty.conf
}

package_kitty-terminfo() {
    pkgdesc='Terminfo for kitty, an OpenGL-based terminal emulator'
    depends=('ncurses')

    mkdir -p "$pkgdir/usr/share/terminfo"
    tic -x -o "$pkgdir/usr/share/terminfo" $pkgbase-$pkgver/terminfo/kitty.terminfo
}

package_kitty-shell-integration() {
    pkgdesc='Shell integration scripts for kitty, an OpenGL-based terminal emulator'
    depends=('python3')
    optdepends+=('bash: Bash completions'
                 'zsh: ZSH completions'
                 'fish: Fish completions')

    mkdir -p "$pkgdir/usr/lib/kitty/"
    cp -r "$srcdir/$pkgbase-$pkgver/shell-integration" "$pkgdir/usr/lib/kitty/"
}
