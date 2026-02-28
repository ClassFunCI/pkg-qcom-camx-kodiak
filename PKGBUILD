# Maintainer: Xilin Wu <sophon@radxa.com>
# Upstream: Qualcomm CamX camera HAL prebuilt for Kodiak (QCM6490)

pkgname=qcom-camx-kodiak
pkgver=1.0.12
pkgrel=3
pkgdesc="Qualcomm CamX camera HAL prebuilt for Kodiak (QCM6490)"
arch=('aarch64')
url="https://softwarecenter.qualcomm.com"
license=('LicenseRef-Qualcomm-Proprietary')
# libprotobuf.so.33.5.0
# libabsl_strings.so.2601.0.0
depends=('qcom-camxlib-kodiak' 'qcom-camxfirmware-kodiak' 'qcom-sensors-prebuilts' 'protobuf-33.5' 'abseil-cpp-20260107')
options=('!strip')

_datestamp=260224.1

backup=('var/cache/camera/camxoverridesettings.txt')
source=("https://qartifactory-edge.qualcomm.com/artifactory/qsc_releases/software/chip/component/camx.qclinux.0.0/${_datestamp}/prebuilt_yocto/camx-kodiak_${pkgver}_armv8-2a.tar.gz"
        "camxoverridesettings.txt")
sha256sums=('734fcff71ca3d863aa7ce3de5a889a92ec96a56d42025abb36dd4c85e99080cf'
            'SKIP')

package() {
  cd "$srcdir"

  # Header
  install -Dm644 usr/include/camx/camxformatutilexternal.h \
    "$pkgdir/usr/include/camx/camxformatutilexternal.h"

  # Libraries under usr/lib/camx/kodiak/ (includes camera/components/ and hw/)
  install -dm755 "$pkgdir/usr/lib/camx/kodiak/camera/components"
  install -dm755 "$pkgdir/usr/lib/camx/kodiak/hw"
  cp -a usr/lib/camx/kodiak/* "$pkgdir/usr/lib/camx/kodiak/"

  # Libraries directly under usr/lib/
  cp -a usr/lib/libcamera_hardware_kodiak.so* "$pkgdir/usr/lib/"
  cp -a usr/lib/libcamxexternalformatutils_kodiak.so* "$pkgdir/usr/lib/"

  # License
  install -Dm644 __LIC__/Qualcomm-Technologies-Inc.-Proprietary \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
  
  install -Dm644 camxoverridesettings.txt \
    "$pkgdir/var/cache/camera/camxoverridesettings.txt"
}
