# Maintainer: Stremio <support@stremio.com>
pkgname=stremio-linux-shell
pkgver=1.0.0beta13
pkgrel=1
pkgdesc="Freedom to Stream - Stremio native Linux client"
arch=('x86_64')
url="https://github.com/Stremio/stremio-linux-shell"
license=('GPL3')
depends=(
    'gtk4'
    'libadwaita'
    'webkitgtk-6.0'
    'mpv'
    'libepoxy'
    'gettext'
    'nodejs'
)
makedepends=(
    'rust'
    'cargo'
    'pkg-config'
)
source=("$pkgname::git+file:///home/kigaita/Documents/git/stremio-linux-shell")
sha256sums=('SKIP')

build() {
    cd "$srcdir/$pkgname"
    cargo build --release --locked
}

package() {
    cd "$srcdir/$pkgname"

    # Binary (installed as stremio-bin, launched via wrapper)
    install -Dm755 "target/release/stremio-linux-shell" "$pkgdir/usr/bin/stremio-bin"

    # Wrapper script that sets SERVER_PATH
    install -dm755 "$pkgdir/usr/bin"
    cat > "$pkgdir/usr/bin/stremio" <<'EOF'
#!/bin/bash
if ls /dev/nvidia0 &>/dev/null 2>&1; then
    export GSK_RENDERER=opengl
fi
export SERVER_PATH=/usr/share/stremio/server.js
exec /usr/bin/stremio-bin "$@"
EOF
    chmod 755 "$pkgdir/usr/bin/stremio"

    # Desktop entry — patch out DBusActivatable and set full Exec path
    install -Dm644 "data/com.stremio.Stremio.desktop" \
        "$pkgdir/usr/share/applications/com.stremio.Stremio.desktop"
    sed -i \
        -e 's|^DBusActivatable=.*||' \
        -e 's|^Exec=.*|Exec=/usr/bin/stremio %u|' \
        "$pkgdir/usr/share/applications/com.stremio.Stremio.desktop"

    # Icons
    install -Dm644 "data/icons/com.stremio.Stremio.svg" \
        "$pkgdir/usr/share/icons/hicolor/scalable/apps/com.stremio.Stremio.svg"

    # D-Bus service — fix path from flatpak /app/bin to /usr/bin wrapper
    install -Dm644 "data/com.stremio.Stremio.service" \
        "$pkgdir/usr/share/dbus-1/services/com.stremio.Stremio.service"
    sed -i 's|Exec=.*|Exec=/usr/bin/stremio --gapplication-service|' \
        "$pkgdir/usr/share/dbus-1/services/com.stremio.Stremio.service"

    # AppStream metainfo
    install -Dm644 "data/com.stremio.Stremio.metainfo.xml" \
        "$pkgdir/usr/share/metainfo/com.stremio.Stremio.metainfo.xml"

    # Server JS
    install -Dm644 "data/server.js" \
        "$pkgdir/usr/share/stremio/server.js"
}