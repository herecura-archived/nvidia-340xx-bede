# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-340xx-bede
pkgver=340.46
_extramodules=3.17-BEDE-external
pkgrel=5
pkgdesc="NVIDIA drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=('linux-bede>=3.17.1' 'linux-bede<3.18' 'linux-bede-headers>=3.17' 'linux-bede-headers<3.18' "nvidia-340xx-utils=$pkgver" "nvidia-340xx-libgl=$pkgver")
conflicts=('nvidia')
license=('custom')
install=nvidia.install
options=(!strip)

source=(
    "http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run"
    "http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run"
)

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    [ -d "$_pkg" ] && rm -rf "$_pkg"
    sh $_pkg.run --extract-only
    cd $_pkg
    # patch if needed
}

build() {
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_pkg/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module
    cd uvm
    make SYSSRC=/usr/lib/modules/"${_kernver}/build" module
}

package() {
    depends=('linux-bede>=3.17' 'linux-bede<3.18' "nvidia-340xx-utils=$pkgver" "nvidia-340xx-libgl=$pkgver")

    install -Dm644 "$srcdir/$_pkg/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"
    install -D -m644 "${srcdir}/${_pkg}/kernel/uvm/nvidia-uvm.ko" \
        "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-uvm.ko"

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    # gzip all modules
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='$_extramodules'/" "$startdir/nvidia.install"
}

sha256sums=('a2e6d779397372dea77466e85ba30a15211c24502687512694604e570db11f87'
            '85e87c5801ef8e9fe30a34aa01cc7f4d3a11f2ab62d2259bb9ff3235ad56ef79')
