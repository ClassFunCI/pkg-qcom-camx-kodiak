# Maintainer: Xilin Wu <sophon@radxa.com>
# Upstream: Qualcomm CamX camera HAL prebuilt for Kodiak (QCM6490)

pkgname=qcom-camx-kodiak
pkgver=1.0.12
pkgrel=4
pkgdesc="Qualcomm CamX camera HAL prebuilt for Kodiak (QCM6490)"
arch=('aarch64')
url="https://softwarecenter.qualcomm.com"
license=('LicenseRef-Qualcomm-Proprietary')
# libprotobuf.so.33.5.0
# libabsl_strings.so.2601.0.0
depends=('qcom-camxlib-kodiak' 'qcom-camxfirmware-kodiak' 'qcom-sensors-prebuilts')
makedepends=('patchelf')
options=('!strip')

_datestamp=260224.1
_protobuf_pkg=protobuf-33.5-33.5-1-aarch64.pkg.tar.xz
_absl_pkg=abseil-cpp-20260107-20260107.1-1-aarch64.pkg.tar.xz

backup=('var/cache/camera/camxoverridesettings.txt')
source=("https://qartifactory-edge.qualcomm.com/artifactory/qsc_releases/software/chip/component/camx.qclinux.0.0/${_datestamp}/prebuilt_yocto/camx-kodiak_${pkgver}_armv8-2a.tar.gz"
        "camxoverridesettings.txt"
        "https://mirror.classfun.cn/arch/aarch64/${_protobuf_pkg}"
        "https://mirror.classfun.cn/arch/aarch64/${_absl_pkg}")
sha256sums=('734fcff71ca3d863aa7ce3de5a889a92ec96a56d42025abb36dd4c85e99080cf'
            'SKIP'
            '47ae252e20b2764153158e3c50c3262de51dbfca0f6da2f56ae94c0a6f8b83f5'
            'b5dc00eb5cffdfc657528835931328335980f9c127ade438195416cee1cbe43b')
noextract=("${_protobuf_pkg}"
           "${_absl_pkg}")

package() {
  cd "$srcdir"
  local _protobuf_srcdir="$srcdir/vendor-protobuf"
  local _absl_srcdir="$srcdir/vendor-absl"
  local _third_party_dir="$pkgdir/usr/lib/camx/kodiak/third_party"
  local _protobuf_pkgfile="$srcdir/${_protobuf_pkg}"
  local _absl_pkgfile="$srcdir/${_absl_pkg}"

  [[ -f "$_protobuf_pkgfile" ]] || _protobuf_pkgfile="$startdir/${_protobuf_pkg}"
  [[ -f "$_absl_pkgfile" ]] || _absl_pkgfile="$startdir/${_absl_pkg}"

  # Header
  install -Dm644 usr/include/camx/camxformatutilexternal.h \
    "$pkgdir/usr/include/camx/camxformatutilexternal.h"

  # Libraries under usr/lib/camx/kodiak/ (includes camera/components/ and hw/)
  install -dm755 "$pkgdir/usr/lib/camx/kodiak/camera/components"
  install -dm755 "$pkgdir/usr/lib/camx/kodiak/hw"
  cp -a --no-preserve=ownership usr/lib/camx/kodiak/* "$pkgdir/usr/lib/camx/kodiak/"

  # Bundle protobuf/abseil privately so CamX keeps using the Qualcomm-tested
  # ABI instead of whichever protobuf/abseil version happens to be installed.
  rm -rf "$_protobuf_srcdir" "$_absl_srcdir"
  install -dm755 "$_protobuf_srcdir" "$_absl_srcdir" "$_third_party_dir"
  bsdtar --no-same-owner -xf "$_protobuf_pkgfile" -C "$_protobuf_srcdir"
  bsdtar --no-same-owner -xf "$_absl_pkgfile" -C "$_absl_srcdir"

  find "$_protobuf_srcdir/usr/lib" -maxdepth 1 -type f \
    \( -name 'libprotobuf*.so*' -o -name 'libutf8*.so*' \) \
    -exec install -m755 -t "$_third_party_dir" {} +
  find "$_absl_srcdir/usr/lib" -maxdepth 1 -type f -name 'libabsl*.so*' \
    -exec install -m755 -t "$_third_party_dir" {} +

  # Libraries directly under usr/lib/
  cp -a --no-preserve=ownership usr/lib/libcamera_hardware_kodiak.so* "$pkgdir/usr/lib/"
  cp -a --no-preserve=ownership usr/lib/libcamxexternalformatutils_kodiak.so* "$pkgdir/usr/lib/"

  patchelf --add-rpath '$ORIGIN/../third_party' \
    "$pkgdir/usr/lib/camx/kodiak/hw/camera.qcom.so.0.1.0"
  find "$_third_party_dir" -maxdepth 1 -type f -name '*.so*' \
    -exec patchelf --set-rpath '$ORIGIN' {} +

  # License
  install -Dm644 __LIC__/Qualcomm-Technologies-Inc.-Proprietary \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
  
  install -Dm644 camxoverridesettings.txt \
    "$pkgdir/var/cache/camera/camxoverridesettings.txt"
}
