# Maintainer: tohmais <callum.wishart@protonmail.com>

_pkgname="hyprland"
pkgname="${_pkgname}-nvidia"
pkgver="0.28.0"
pkgrel=1
pkgdesc="A dynamic tiling Wayland compositor based on wlroots that doesn't sacrifice on its looks. (NVIDIA patch)"
arch=(any)
url="https://github.com/hyprwm/Hyprland"
license=('BSD')
depends=(
	libxcb
	xcb-proto
	xcb-util
	xcb-util-keysyms
	libxfixes
	libx11
	libxcomposite
	xorg-xinput
	libxrender
	pixman
	wayland-protocols
	cairo
	pango
	polkit
	glslang
	libinput
	libxcb
	libxkbcommon
	opengl-driver
	pixman
	wayland
	xcb-util-errors
	xcb-util-renderutil
	xcb-util-wm
	seatd
	vulkan-icd-loader
	vulkan-validation-layers
	xorg-xwayland
  libliftoff
	libdisplay-info)
makedepends=(
	git
	cmake
	ninja
	gcc
	gdb
	meson
	vulkan-headers
	wayland-protocols
	xorgproto)
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/hyprwm/Hyprland/releases/download/v${pkgver}/source-v${pkgver}.tar.gz"
        "wallpaper.png::https://raw.githubusercontent.com/joshuajeschek/.dotfiles/main/.wallpaper.png"
        "nvidia.patch")
conflicts=("${_pkgname}")
provides=(hyprland)
sha256sums=('baf355cb4e301b27de59fa5fc503b7338eed1bc921fcf76b73629504a00ffefd'
            'df0604a39532b51d0b43fd45032a800586a422591c77296ca60afd10dee3dc10'
            '10223a97a622ea7e641b4305a9a4f7bf0cb0526dacfbfa5970171558b3a63488')
options=(!makeflags !buildflags !strip)

build() {
	cd "$srcdir/hyprland-source"
	make fixwlr
	patch --directory="$srcdir/hyprland-source/subprojects/wlroots/" --forward --strip=0 \
		--input="${srcdir}/nvidia.patch"
	cd "./subprojects/wlroots/" && meson build/ --prefix="${srcdir}/tmpwlr" --buildtype=release && ninja -C build/ && mkdir -p "${srcdir}/tmpwlr" && ninja -C build/ install && cd ../
    cd udis86 && cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -H./ -B./build -G Ninja && cmake --build ./build --config Release --target all -j$(shell nproc) && cd ../..
	make protocols
    make release
	cd ./hyprctl && make all && cd ..
}

package() {
	cd "$srcdir"
	mkdir -p "${pkgdir}/usr/share/wayland-sessions"
	mkdir -p "${pkgdir}/usr/share/hyprland"
	install -Dm755 hyprland-source/build/Hyprland -t "${pkgdir}/usr/bin"
	install -Dm755 hyprland-source/hyprctl/hyprctl -t "${pkgdir}/usr/bin"
	install -Dm644 hyprland-source/assets/*.png -t "${pkgdir}/usr/share/hyprland"
	install -Dm644 hyprland-source/example/hyprland.desktop -t "${pkgdir}/usr/share/wayland-sessions"
	install -Dm644 hyprland-source/example/hyprland.conf -t "${pkgdir}/usr/share/hyprland"
	install -Dm644 hyprland-source/LICENSE -t "${pkgdir}/usr/share/licenses/${_pkgname}"
	install -Dm755 "${srcdir}/tmpwlr/lib/libwlroots.so.12032" -t "${pkgdir}/usr/lib"
}
