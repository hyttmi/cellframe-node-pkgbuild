# Maintainer: Mika Hyttinen <mika dot hyttinen+arch ät gmail dot com>
pkgname=cellframe-node
pkgver=5.3.175
pkgrel=1
pkgdesc="Cellframe blockchain node with a powerful SDK"
arch=('x86_64' 'aarch64')
url="https://cellframe.net"
license=('LGPL3')
makedepends=(git cmake python3 libxslt)
depends=(logrotate libxcrypt-compat)
provides=("cellframe-node" "cellframe-node-cli" "cellframe-node-tool" "cellframe-node-config")
source=(git+https://gitlab.demlabs.net/cellframe/$pkgname.git#commit=872834b849e4b47abc1da34a80479612ab590138
		cellframe-node.logrotate
		cellframe-node.service
		cellframe-node-asan.service
		cellframe-node-tmpfiles.conf
		cellframe-node-sysusers.conf)
md5sums=('SKIP'
         '6a52220e0b285dc9e803082f36897ad4'
         '4bf9cc7596903ffa5aba7fa7922d9016'
         'da837da689d3741cae9366eefc86d9b3'
         '0c7b8ad861f3aac1bce6ffb3a9c15ea1'
         'ecead745d3492224d2a5a2f7d9d561b0')
options=(!debug !strip)
install=$pkgname.install

prepare() {
	sed -i 's|url = \.\./\.\./|url = https://gitlab.demlabs.net/|g' "$srcdir/$pkgname/.gitmodules"
	sed -i 's|url = \.\./|url = https://gitlab.demlabs.net/cellframe/|g' "$srcdir/$pkgname/.gitmodules"
	cd "$pkgname" && git submodule update --init --recursive --progress
}

build() {
	cd "$pkgname"

	if [ -n "$DAP_BUILD_DEBUG" ]; then
        BUILD_TYPE="Debug"
    else
        BUILD_TYPE="Release"
    fi

	cmake -B build \
		-DCMAKE_BUILD_TYPE=$BUILD_TYPE \
		-DCMAKE_C_FLAGS="-fpermissive" \
        -Wno-dev && \
	cmake --build build -j$(nproc)
}

package() {
	cd "$pkgname"
	DESTDIR="$pkgdir" cmake --install build
	mkdir -p "$pkgdir/usr/bin"

	install -Dm644 "$srcdir/$pkgname/LICENSE" -t "$pkgdir/usr/share/licenses/$pkgname"
	install -Dm644 "$srcdir/$pkgname.logrotate" "$pkgdir/etc/logrotate.d/$pkgname"
	install -Dm644 "$srcdir/$pkgname-tmpfiles.conf" "$pkgdir/usr/lib/tmpfiles.d/$pkgname.conf"
	install -Dm644 "$srcdir/$pkgname-sysusers.conf" "$pkgdir/usr/lib/sysusers.d/$pkgname.conf"

	if [ -n "$DAP_BUILD_DEBUG" ] && [ -n "$DAP_ASAN" ]; then
		install -Dm644 "$srcdir/$pkgname-asan.service" "$pkgdir/usr/lib/systemd/system/$pkgname.service"
	else
		install -Dm644 "$srcdir/$pkgname.service" -t "$pkgdir/usr/lib/systemd/system"
	fi

	for _executables in cellframe-node-cli cellframe-node-tool cellframe-node cellframe-node-config
	do
		ln -sf "$pkgdir/opt/cellframe-node/bin/$_executables" "$pkgdir/usr/bin/$_executables"
	done
}
