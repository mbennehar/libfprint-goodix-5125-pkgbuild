# Maintainer: Moussab
# Contributor: Berke Kabagöz <berkekbgz@gmail.com> (Goodix driver author)
# Note: This Arch PKGBUILD was generated with AI assistance and verified on hardware.

pkgname=libfprint-goodix-5125-git
_pkgname=libfprint
pkgver=3.0.0.r916.g595320a
pkgrel=1
pkgdesc="Library for fingerprint readers with Goodix 27c6:5125 sensor support"
arch=('x86_64')
url="https://github.com/sorguido/goodix-27c6-5125"
license=('LGPL-2.1-or-later')
depends=(
  'glib2'
  'glibc'
  'libgcc'
  'libgudev'
  'libgusb'
  'openssl'
  'pixman'
)
makedepends=(
  'git'
  'glib2-devel'
  'gobject-introspection'
  'meson'
  'ninja'
  'python-cairo'
  'python-gobject'
  'systemd'
)
provides=(
  "libfprint=$pkgver"
  'libfprint-2.so'
)
conflicts=('libfprint')
source=("goodix-27c6-5125::git+https://github.com/sorguido/goodix-27c6-5125.git")
sha256sums=('SKIP')

pkgver() {
  cd "$srcdir/goodix-27c6-5125"
  local ver
  ver=$(cat VERSION 2>/dev/null || echo "3.0.0")
  printf "%s.r%s.g%s" "$ver" "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
}

build() {
  cd "$srcdir/goodix-27c6-5125"

  rm -rf build-chicago build

  msg2 "Building Chicago native biometric matcher..."
  python3 -c "
import importlib.util
from pathlib import Path
import subprocess

root = Path('.').resolve()
spec = importlib.util.spec_from_file_location('goodix_native_builder', root / 'production/build-chicago-native.py')
native = importlib.util.module_from_spec(spec)
spec.loader.exec_module(native)

work = root / 'build-chicago'
work.mkdir(exist_ok=True)

def run(*args):
    res = subprocess.run([str(a) for a in args], text=True, capture_output=True)
    if res.returncode != 0:
        raise RuntimeError(res.stderr or res.stdout)
    return res.stdout.strip()

archive, inc, prov = native.build_native(root, work, run)
"

  local native_archive="$PWD/build-chicago/native/libgoodix-chicago-native.a"
  local native_inc="$PWD/production/chicago-native/chicago"

  msg2 "Configuring libfprint with Goodix 27c6:5125 driver..."
  local meson_options=(
    --prefix=/usr
    --libdir=lib
    --buildtype=release
    --wrap-mode=nodownload
    -D drivers=default
    -D introspection=true
    -D doc=false
    -D installed-tests=false
    -D udev_rules=enabled
    -D udev_rules_dir=/usr/lib/udev/rules.d
    -D udev_hwdb=enabled
    -D udev_hwdb_dir=/usr/lib/udev/hwdb.d
    -D goodix_production_minimal=true
    -D goodix_chicago=true
    -D goodix_chicago_native_archive="$native_archive"
    -D goodix_chicago_native_include="$native_inc"
    -D c_args='["-DGOODIX_PRODUCTION_FPRINTD_ACTION_PROFILE"]'
  )

  meson setup build reference/libfprint-fedora44-1.94.100/source "${meson_options[@]}"
  meson compile -C build
}

package() {
  cd "$srcdir/goodix-27c6-5125"
  DESTDIR="$pkgdir" meson install -C build
}
