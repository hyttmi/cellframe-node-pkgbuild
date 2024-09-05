# Maintainer: Mika Hyttinen <mika dot hyttinen+arch ät gmail dot com>
pkgname=cellframe-node
pkgver=5.3.286
pkgrel=2
pkgdesc='Cellframe blockchain node with a powerful SDK'
arch=('x86_64' 'aarch64')
url='https://cellframe.net'
license=('LGPL3')
makedepends=(git cmake python3 libxslt)
depends=(logrotate libxcrypt-compat)
provides=('cellframe-node' 'cellframe-node-cli' 'cellframe-node-tool' 'cellframe-node-config')
replaces=('cellframe-node-debug')
source=(git+https://gitlab.demlabs.net/cellframe/$pkgname.git#commit=e8aef7273f6088a89fc578d0fcfc93bed5fe191d
		cellframe-node.logrotate
		cellframe-node.service
		cellframe-node-asan.service
		cellframe-node-tmpfiles.conf
		cellframe-node-sysusers.conf)
md5sums=('SKIP'
         'a10650eb138f6fe0c4bbefa6557ffb4f'
         '4bf9cc7596903ffa5aba7fa7922d9016'
         'da837da689d3741cae9366eefc86d9b3'
         'ac930b427a0805dd9c2c1fabe45048fc'
         'ecead745d3492224d2a5a2f7d9d561b0')
options=(!debug !strip)
install=$pkgname.install

prepare() {
	sed -i 's|url = \.\./\.\./|url = https://gitlab.demlabs.net/|g' "$srcdir/$pkgname/.gitmodules"
	sed -i 's|url = \.\./|url = https://gitlab.demlabs.net/cellframe/|g' "$srcdir/$pkgname/.gitmodules"
	cd "$pkgname" && git submodule update --init --recursive --progress
	VERSION_MAJOR=$(echo "$pkgver" | cut -d '.' -f1)
	VERSION_MINOR=$(echo "$pkgver" | cut -d '.' -f2)
	VERSION_PATCH=$(echo "$pkgver" | cut -d '.' -f3)
	sed -i "s|^VERSION_MAJOR=.*|VERSION_MAJOR=$VERSION_MAJOR|" "$srcdir/$pkgname/version.mk"
	sed -i "s|^VERSION_MINOR=.*|VERSION_MINOR=$VERSION_MINOR|" "$srcdir/$pkgname/version.mk"
	sed -i "s|^VERSION_PATCH=.*|VERSION_PATCH=$VERSION_PATCH|" "$srcdir/$pkgname/version.mk"
}

build() {

	if [ -n "$DAP_BUILD_DEBUG" ] && [ -n "$DAP_ASAN" ]; then
		cp "$srcdir/$pkgname-asan.service" "$srcdir/$pkgname/dist.linux/share/$pkgname.service"
	else
		cp "$srcdir/$pkgname.service" "$srcdir/$pkgname/dist.linux/share/$pkgname.service"
	fi

	cd "$pkgname"

	if [ -n "$DAP_BUILD_DEBUG" ]; then
        BUILD_TYPE="Debug"
    else
        BUILD_TYPE="Release"
    fi

	cmake -B build \
    -DCMAKE_BUILD_TYPE=$BUILD_TYPE \
	-DCMAKE_C_FLAGS="-fpermissive" \
    -Wno-dev

	if [ -n "$CELLFRAME_NO_OPTIMIZATION" ]; then
	    cmake -B build -DCELLFRAME_NO_OPTIMIZATION=ON
	fi

	cmake --build build --clean-first
}

package() {
	cd "$pkgname"
	DESTDIR="$pkgdir" cmake --install build
	mkdir -p "$pkgdir/usr/bin"

	install -Dm644 "$srcdir/$pkgname/LICENSE" -t "$pkgdir/usr/share/licenses/$pkgname"
	install -Dm644 "$srcdir/$pkgname.logrotate" "$pkgdir/etc/logrotate.d/$pkgname"
	install -Dm644 "$srcdir/$pkgname-tmpfiles.conf" "$pkgdir/usr/lib/tmpfiles.d/$pkgname.conf"
	install -Dm644 "$srcdir/$pkgname-sysusers.conf" "$pkgdir/usr/lib/sysusers.d/$pkgname.conf"

	for _executables in cellframe-node-cli cellframe-node-tool cellframe-node cellframe-node-config
	do
		ln -sf "/opt/cellframe-node/bin/$_executables" "$pkgdir/usr/bin/$_executables"
	done
}
