# Maintainer: Nk-YMZ <village_flute (at) outlook (dot) com>
# Contributor: Iyán Méndez Veiga <me (at) iyanmv (dot) com>
pkgname=lenovo-wwan-unlock
pkgver=4.0.0
pkgrel=1
pkgdesc="FCC and DPR unlock for Lenovo PCs"
arch=('x86_64')
url="https://github.com/lenovo/lenovo-wwan-unlock"
license=('LicenseRef-lenovo-software-code-license')
depends=(
    'gcc-libs'
    'glib2'
    'glibc'
    'libmbim'
    'libxml2-legacy'
    'modemmanager'
    'openssl'
    'pciutils'
    'zlib'
)
optdepends=('android-tools: fastboot support required for the Rolling Wireless RW101R-GL modem (USB IDs 33f8:0301/33f8:0302)')
options=('!debug' '!strip')
install=$pkgname.install
source=("$pkgname-$pkgver.tar.gz::https://github.com/lenovo/$pkgname/archive/refs/tags/v$pkgver.tar.gz")
sha256sums=('08e871e1413f6e9a815ae4e254449b893a1fe637c19f33d3e7cee42dc16871bf')

package() {
    cd $pkgname-$pkgver

    # FCC unlock hooks for ModemManager (all supported modems)
    tar -xzf fcc-unlock.d.tar.gz --no-same-owner
    install -Dm755 fcc-unlock.d/* -t "$pkgdir"/usr/lib/ModemManager/fcc-unlock.d/

    # SAR config files (recursive, keeps cs25/, cs26/ and cs26/fxn/)
    install -d "$pkgdir"/opt/fcc_lenovo
    tar -xzf sar_config_files.tar.gz -C "$pkgdir"/opt/fcc_lenovo --no-same-owner

    # DEV OTA package
    tar -xzf DEV_OTA_PACKAGE.tar.gz -C "$pkgdir"/opt/fcc_lenovo --no-same-owner

    # Sanitize permissions of the extracted data: dirs 755, files 644
    find "$pkgdir"/opt/fcc_lenovo/sar_config_files "$pkgdir"/opt/fcc_lenovo/DEV_OTA_PACKAGE \
        -type d -exec chmod 755 {} +
    find "$pkgdir"/opt/fcc_lenovo/sar_config_files "$pkgdir"/opt/fcc_lenovo/DEV_OTA_PACKAGE \
        -type f -exec chmod 644 {} +

    # Libraries
    install -Dm755 lib*.so lib*.so.* -t "$pkgdir"/opt/fcc_lenovo/lib/

    # Executables
    install -Dm755 DPR_Fcc_unlock_service -t "$pkgdir"/opt/fcc_lenovo/
    install -Dm755 configservice_lenovo -t "$pkgdir"/opt/fcc_lenovo/

    # Systemd service
    install -Dm644 lenovo-cfgservice.service -t "$pkgdir"/usr/lib/systemd/system/

    # Lenovo license, agreement and third party notices
    install -Dm644 "Lenovo Software Code License Agreement for wwan.txt" \
        "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
    install -Dm644 "Lenovo Licence Agreement.pdf" \
        "$pkgdir"/usr/share/licenses/$pkgname/Lenovo-Licence-Agreement.pdf
    install -Dm644 ThirdPartyNotices.txt -t "$pkgdir"/usr/share/licenses/$pkgname/
}
