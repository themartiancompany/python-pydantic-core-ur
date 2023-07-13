# Maintainer: David Runge <dvzrv@archlinux.org>

_name=pydantic-core
pkgname=python-pydantic-core
pkgver=2.1.2
epoch=1
pkgrel=1
pkgdesc="Core validation logic for pydantic written in rust "
arch=(x86_64)
url="https://github.com/pydantic/pydantic-core"
license=(MIT)
depends=(
  gcc-libs
  glibc
  python
  python-typing-extensions
)
makedepends=(
  python-build
  python-installer
  python-maturin
)
checkdepends=(
  python-dirty-equals
  python-hypothesis
  python-pytest
  python-pytest-examples
  python-pytest-mock
  python-pytest-timeout
)
options=(!lto)
source=($pkgname-$pkgver.tar.gz::$url/archive/refs/tags/v$pkgver.tar.gz)
sha512sums=('8d251f26eedc077cd569aaa14e2548005cbe3208194992667925e652105b9ee5c5cb1b2049b6bdbfa1000f13f52ed87a3fce577bbd53119da7b0216fdeed5e18')
b2sums=('54bb89853b09ba773bc3070ed17286416b2fa8dbd240e72659a7dfbebd777c636969ab07d22b63218239b5d3a282071187c1580bdee3fa7d041b8199bc4dfc1f')

prepare() {
  # we don't support version pinning
  sed -e 's/,!=4.7.0//g' -i $_name-$pkgver/pyproject.toml
  # remove pytest benchmark options
  sed -e '/benchmark/d' -i $_name-$pkgver/pyproject.toml
}

build() {
  cd $_name-$pkgver
  python -m build --wheel --no-isolation
}

check() {
  local pytest_options=(
    -vv
    --ignore tests/benchmarks  # don't run benchmark tests
    --deselect tests/test_hypothesis.py::test_recursive  # recursion error?!
  )
  local site_packages=$(python -c "import site; print(site.getsitepackages()[0])")

  cd $_name-$pkgver
  # install to temporary location, as importlib is used
  python -m installer --destdir=test_dir dist/*.whl
  export PYTHONPATH="$PWD/test_dir/$site_packages:$PYTHONPATH"
  HYPOTHESIS_PROFILE=slow pytest "${pytest_options[@]}"
}

package() {
  cd $_name-$pkgver
  python -m installer --destdir="$pkgdir" dist/*.whl
  install -vDm 644 LICENSE -t "$pkgdir/usr/share/licenses/$pkgname/"
  install -vDm 644 README.md -t "$pkgdir/usr/share/doc/$pkgname/"
}
