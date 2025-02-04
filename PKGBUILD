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
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: David Runge <dvzrv@archlinux.org>

_py="python"
_proj="pydantic"
_pkg="${_proj}-core"
pkgname="${_py}-${_pkg}"
# WARNING: this package is pinned down
#          to the patch-level version in
#          python-pydantic and should only
#          be updated in lock-step with it
pkgver=2.14.6
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
  'gcc-libs'
  'glibc'
  "${_py}"
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
  '08d11343ccbda5857bbd256598a7153dd1e57ce7ef1016436c5cf45adde0b7fb1a1e3cffcb0006982bbab5d25fc3f2f2d1161fbf3f979b4222e9573faf73c8d3'
)
b2sums=(
  'c9c9089c8888f0725c1b3cec301d6b03febb4c2ea8f501047c741301c6ce3c0a767fe458121104cc46e34ebad5fd38918d5fd238949dcaf74ecb41320fb29146'
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
