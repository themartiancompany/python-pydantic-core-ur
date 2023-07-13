# Maintainer: David Runge <dvzrv@archlinux.org>

_name=pydantic-core
pkgname=python-pydantic-core
pkgver=2.3.0
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
sha512sums=('976e1ad2c13d1431c7fdac0aae559dd261f949afad6c3c5c97e19fac9d196abb030bc2713cd36105bb298a7c63b633a8bce5d237402d15a630626b2bc9b3d5df')
b2sums=('48057f1b27567a3eb93f745d44fbb6075da2d388b943983c32c67ad195e4412ca95f06a74ccffed0653747c9a0b0bd637cb433c2f525fccc18f133c7a1b3bc72')

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
