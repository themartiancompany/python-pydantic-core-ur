# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer: David Runge <dvzrv@archlinux.org>

_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _libc="ndk-sysroot"
  _libc_libs="libcompiler-rt"
elif [[ "${_os}" == "GNU/Linux" ]]; then
  _libc_libs="gcc-libs"
  _libc="glibc"
fi
_py="python"
_pyver="$( \
  "${_py}" \
    -V | \
    awk \
      '{print $2}')"
_pymajver="${_pyver%.*}"
_pyminver="${_pymajver#*.}"
_pynextver="${_pymajver%.*}.$(( \
  ${_pyminver} + 1))"
_proj="pydantic"
_pkg="${_proj}-core"
pkgname="${_py}-${_pkg}"
# WARNING: this package is pinned down
#          to the patch-level version in
#          python-pydantic and should only
#          be updated in lock-step with it
pkgver=2.27.2
pkgrel=1
epoch=1
_pkgdesc=(
  "Core validation logic for"
  "pydantic written in rust "
)
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'armv7l'
  'mips'
  'pentium4'
  'i686'
)
_http="https://github.com"
_ns="${_proj}"
url="${_http}/${_ns}/${_pkg}"
license=(
  'MIT'
)
depends=(
  "${_libc}"
  "${_libc_libs}"
  "${_py}>=${_pymajver}"
  "${_py}<${_pynextver}"
  "${_py}-typing-extensions"
)
makedepends=(
  "${_py}-build"
  "${_py}-installer"
  "${_py}-maturin"
)
checkdepends=(
  "${_py}-dirty-equals"
  "${_py}-hypothesis"
  "${_py}-pytest"
  "${_py}-pytest-benchmark"
  "${_py}-pytest-examples"
  "${_py}-pytest-mock"
  "${_py}-pytest-timeout"
)
options=(
  !lto
)
source=(
  "${pkgname}-${pkgver}.tar.gz::${url}/archive/refs/tags/v${pkgver}.tar.gz"
)
sha512sums=(
  '2720fe30c074b654bad2f200786962f3d92300de01b757b3a6a892cc3a2cec0693fd0070cee8d27a6c83baba68a29a6fd049cdf2d4fdd50bc07312ef4f3cf47c'
)
b2sums=(
  '2b338b3e2d7dd52b8aaa072e06513ec1f5dd10e6699acd3b01cb0e6a7e20d2181a6be233dc51b3d6d4b50a2ea1c19ac66d66c62965fff4b51a94146dbf5eddcc'
)

prepare() {
  # we don't support
  # version pinning
  sed \
    -e \
    's/,!=4.7.0//g' \
    -i \
    "${_pkg}-${pkgver}/pyproject.toml"
}

build() {
  cd \
    "${_pkg}-${pkgver}"
  "${_py}" \
    -m \
      build \
    --wheel \
    --no-isolation
}

check() {
  local \
    pytest_options=() \
    site_packages
  pytest_options=(
    -vv
  )
  site_packages="$( \
    "${_py}" \
      -c \
        "import site; print(site.getsitepackages()[0])")"
  cd \
    "${_pkg}-${pkgver}"
  # install to temporary location, as importlib is used
  "${_py}" \
    -m \
      installer \
        --destdir="test_dir" \
        "dist/"*".whl"
  export \
    PYTHONPATH="${PWD}/test_dir/${site_packages}:${PYTHONPATH}"
  HYPOTHESIS_PROFILE=slow \
  pytest \
    "${pytest_options[@]}"
}

package() {
  cd \
    "${_pkg}-${pkgver}"
  "${_py}" \
    -m \
      installer \
        --destdir="${pkgdir}" \
        "dist/"*".whl"
  install \
    -vDm644 \
    "LICENSE" \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
  install \
    -vDm644 \
    "README.md" \
    -t \
    "${pkgdir}/usr/share/doc/${pkgname}/"
}

# vim:set sw=2 sts=-1 et:
