# Maintainer: Rudra Saraswat <rs2009@ubuntu.com>

pkgname='blend-web-store-git'
pkgver=r12.fa8f99f
pkgrel=1
_electronversion=22
pkgdesc="A web store designed for blendOS"
arch=('x86_64' 'i686')
url="https://github.com/blend-os/web-store"
license=('GPL3')
depends=("electron${_electronversion}")
provides=("${pkgname%-git}")
conflicts=("${pkgname%-git}")
makedepends=("electron${_electronversion}" 'git' 'npm')
source=('git+https://github.com/blend-os/web-store.git'
        'blend-web-store.desktop'
        'blend-web-store')
sha256sums=('SKIP'
            '8efa81795f5a72d55ae9d9284467ea4ea4abdbe5101d4516e972ec39a44ce4ba'
            '1da4a07a9bfa1bbd23a65cd45b8c45e82bc701ccaa5387dd6927a017ead88096')

pkgver() {
    cd "${srcdir}/web-store"
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
    cd "${srcdir}/web-store"
    npm config set cache "${srcdir}/npm-cache"
    npm install
}

build() {
    cd "${srcdir}/web-store"
    npm config set cache "${srcdir}/npm-cache"
    export NODE_ENV=production
    electronDist="/usr/lib/electron${_electronversion}"
    electronVer="$(sed s/^v// /usr/lib/electron${_electronversion}/version)"
    npm run icons
    npm run pack -- -c.electronDist=${electronDist} \
        -c.electronVersion=${electronVer} --publish never
}

package() {
    cd "${srcdir}/web-store"

    local _arch
    case ${CARCH} in
    i686)
        _arch=linux-ia32-unpacked
        ;;
    x86_64)
        _arch=linux-unpacked
        ;;
    *)
        _arch=linux-${CARCH}-unpacked
        ;;
    esac

    install -Dm644 "dist/${_arch}/resources/app.asar" \
        "$pkgdir/usr/lib/${pkgname%-git}/${pkgname%-git}.asar"

    for icon_size in 16 24 32 48 64 128 256 512; do
        install -Dm644 "build/icons/png/${icon_size}x${icon_size}.png" \
            "${pkgdir}/usr/share/icons/hicolor/${icon_size}x${icon_size}/apps/${pkgname%-git}.png"
    done

    install -Dm644 "${srcdir}/${pkgname%-git}.desktop" -t \
        "${pkgdir}"/usr/share/applications/
    install -Dm755 "${srcdir}/${pkgname%-git}" -t "${pkgdir}"/usr/bin/
    install -Dm755 "${srcdir}/web-store/wapp-inst/blend-wapp-inst" -t "${pkgdir}"/usr/bin/
}
