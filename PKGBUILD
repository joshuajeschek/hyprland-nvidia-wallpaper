# Maintainer: tohmais <callum.wishart@protonmail.com>

_pkgname="hyprland"
pkgname="${_pkgname}-nvidia"
pkgver="0.21.0beta"
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
	xorg-xwayland)
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
        "nvidia.patch")
conflicts=("${_pkgname}")
provides=(hyprland)
sha256sums=('babc64cde2f56e4e77c02f81862a2879e097086b074ebaa533a1bcf0a87dfb23'
            '522b19656d7c1627ec615b6720182590570560e346c1670f9df002015707b340')
options=(!makeflags !buildflags !strip)

build() {
	cd "$srcdir/hyprland-source"
	make fixwlr
	patch --directory="$srcdir/hyprland-source/subprojects/wlroots/" --forward --strip=0 \
		--input="${srcdir}/../nvidia.patch"
	cd "./subprojects/wlroots/" && meson build/ --prefix="${srcdir}/tmpwlr" --buildtype=release && ninja -C build/ && mkdir -p "${srcdir}/tmpwlr" && ninja -C build/ install && cd ../../
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
