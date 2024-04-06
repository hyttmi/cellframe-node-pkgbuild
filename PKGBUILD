# Maintainer: Mika Hyttinen <mika dot hyttinen+arch ät gmail dot com>
pkgname=cellframe-node
pkgver=5.2.490
pkgrel=1
pkgdesc="Cellframe blockchain node with a powerful SDK"
arch=('x86_64' 'aarch64')
url="https://cellframe.net"
license=('LGPL3')
makedepends=(git cmake python3)
depends=(logrotate libxcrypt-compat)
provides=("cellframe-node" "cellframe-node-cli" "cellframe-node-tool")
source=(git+https://gitlab.demlabs.net/cellframe/$pkgname.git#commit=4a230d2f14bae3da29d72f258435a2b5d4b1a71c
		git+https://gitlab.demlabs.net/cellframe/cellframe-sdk.git#commit=b086a5c64a7e4cb0a4cebc37ff9d85e2de952693
		git+https://gitlab.demlabs.net/dap/dap-sdk.git#commit=087b8536e3f8779ab51f288af85f0c71b652e3bc
		git+https://gitlab.demlabs.net/cellframe/python-cellframe.git#commit=ed5e4b338052f1897164519c30392a838ce405f4
		cellframe-node.logrotate
		cellframe-node-logrotate.service
		cellframe-node-logrotate.timer
		cellframe-node.service)
md5sums=('485f1111843276340b0f1faf87abc3d0'
         '23343c3c8be3363a5ac2b78a85eb96dc'
         '6d8313251c773f8e8d2bce61636da94b'
         'db753282b4e1647387fab277598c79ca'
         '6a52220e0b285dc9e803082f36897ad4'
         '7c1087eea7336d99c4af55119673b009'
         '47edb0d55d537e72f3de07ec6a72ea78'
         'd1468c5407f058d7d7c7881ef8f74516')
install=$pkgname.install
conflicts=(cellframe-dashboard cellframe-wallet cellframe-node-debug)
options=(!debug)

prepare() {
	rm -rf "$srcdir/$pkgname/cellframe-sdk"
	rm -rf "$srcdir/$pkgname/python-cellframe"
	rm -rf "$srcdir/$pkgname/dap-sdk"
	ln -sf "$srcdir/cellframe-sdk" "$srcdir/$pkgname/cellframe-sdk"
	ln -sf "$srcdir/python-cellframe" "$srcdir/$pkgname/python-cellframe"
	ln -sf "$srcdir/dap-sdk" "$srcdir/$pkgname/dap-sdk"
}

build() {
	cd "$pkgname"
	cmake -B build \
		-DCMAKE_BUILD_TYPE='Release' \
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
