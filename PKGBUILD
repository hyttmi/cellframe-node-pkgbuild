# Maintainer: Mika Hyttinen <mika dot hyttinen+arch ät gmail dot com>
pkgname=cellframe-node
pkgver=5.2.531
pkgrel=1
pkgdesc="Cellframe blockchain node with a powerful SDK"
arch=('x86_64' 'aarch64')
url="https://cellframe.net"
license=('LGPL3')
makedepends=(git cmake python3)
depends=(logrotate libxcrypt-compat)
provides=("cellframe-node" "cellframe-node-cli" "cellframe-node-tool")
source=(git+https://gitlab.demlabs.net/cellframe/$pkgname.git#commit=27018546840f8f436dfc5b26656144d1e4516598
		git+https://gitlab.demlabs.net/cellframe/cellframe-sdk.git#commit=12f56f864303e118dbaddb911ec224f1edba6aab
		git+https://gitlab.demlabs.net/dap/dap-sdk.git#commit=884b2f047357e09b321bffb056e4d19c0ea39c3b
		git+https://gitlab.demlabs.net/cellframe/python-cellframe.git#commit=d65ff06a8c08ed1f1352ea8ec13a6af4a707d68f
		cellframe-node.logrotate
		cellframe-node-logrotate.service
		cellframe-node-logrotate.timer
		cellframe-node.service)
md5sums=('SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         '6a52220e0b285dc9e803082f36897ad4'
         '7c1087eea7336d99c4af55119673b009'
         '47edb0d55d537e72f3de07ec6a72ea78'
         '72472d529b38f06a78f37ac659b18d65')
install=$pkgname.install
conflicts=(cellframe-dashboard cellframe-wallet cellframe-node-debug)
options=(!debug)

prepare() {
	rm -rf "$srcdir/$pkgname/cellframe-sdk"
	rm -rf "$srcdir/$pkgname/python-cellframe"
	rm -rf "$srcdir/$pkgname/dap-sdk"
	mv "$srcdir/cellframe-sdk" "$srcdir/$pkgname/cellframe-sdk"
	mv "$srcdir/python-cellframe" "$srcdir/$pkgname/python-cellframe"
	mv "$srcdir/dap-sdk" "$srcdir/$pkgname/dap-sdk"
}

build() {
	cd "$pkgname"
	cmake -B build \
		-DCMAKE_BUILD_TYPE='Release' \
		-DCMAKE_C_FLAGS="-fpermissive" \
        -Wno-dev
	cmake --build build -j$(nproc)
}

package() {
	cd "$pkgname"
	
	DESTDIR="$pkgdir" cmake --install build

	install -Dm 644 "$srcdir/$pkgname/LICENSE" -t "$pkgdir/usr/share/licenses/$pkgname"

	install -Dm 644 "$srcdir/$pkgname.logrotate" "$pkgdir/etc/logrotate.d/$pkgname"

	install -Dm 644 "$srcdir/$pkgname.service" -t "$pkgdir/usr/lib/systemd/system"

	install -Dm 644 "$srcdir/$pkgname-logrotate.service" -t "$pkgdir/usr/lib/systemd/system"
	
	install -Dm 644 "$srcdir/$pkgname-logrotate.timer" -t "$pkgdir/usr/lib/systemd/system"
}
