# Based on AUR pkgbuild from Eric Engestrom <aur [at] engestrom [dot] ch>
# Modified for multilib by: Tk-Glitch <ti3nou at gmail dot com>

plain '       .---.`               `.---.'
plain '    `/syhhhyso-           -osyhhhys/`'
plain '   .syNMdhNNhss/``.---.``/sshNNhdMNys.'
plain '   +sdMh.`+MNsssssssssssssssNM+`.hMds+'
plain '   :syNNdhNNhssssssssssssssshNNhdNNys:'
plain '    /ssyhhhysssssssssssssssssyhhhyss/'
plain '    .ossssssssssssssssssssssssssssso.'
plain '   :sssssssssssssssssssssssssssssssss:'
plain '  /sssssssssssssssssssssssssssssssssss/'
plain ' :sssssssssssssoosssssssoosssssssssssss:'
plain ' osssssssssssssoosssssssoossssssssssssso'
plain ' osssssssssssyyyyhhhhhhhyyyyssssssssssso'
plain ' /yyyyyyhhdmmmmNNNNNNNNNNNmmmmdhhyyyyyy/'
plain '  smmmNNNNNNNNNNNNNNNNNNNNNNNNNNNNNmmms'
plain '   /dNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNd/'
plain '    `:sdNNNNNNNNNNNNNNNNNNNNNNNNNds:`'
plain '       `-+shdNNNNNNNNNNNNNNNdhs+-`'
plain '             `.-:///////:-.`'

_NUKR="true"

pkgname=('spirv-tools-tkg-git' 'lib32-spirv-tools-tkg-git')
pkgver=2023.5.rc1.r38.ge03c8f5c8
pkgrel=1
pkgdesc='API and commands for processing SPIR-V modules'
url='https://github.com/KhronosGroup/SPIRV-Tools'
arch=('i686' 'x86_64')
license=('MIT')
source=('git+https://github.com/KhronosGroup/SPIRV-Tools.git#branch=main'
        'git+https://github.com/KhronosGroup/SPIRV-Headers.git#branch=main')
sha1sums=('SKIP'
          'SKIP')
makedepends=('cmake' 'git' 'python' 'gcc-libs' 'lib32-gcc-libs' 'ninja')
options=('staticlibs')

pkgver() {
  cd "${srcdir}"/SPIRV-Tools
  git describe --long --tags --always | sed 's/\([^-]*-g\)/r\1/;s/-/./g;s/^v//'
}

build() {
  cd "${srcdir}"/SPIRV-Tools

  msg "Cleaning source code tree..."
  git reset --hard HEAD
  git clean -xdf

  mkdir build64
  mkdir build32

  cd "${srcdir}"/SPIRV-Tools/build64
  cmake .. \
      -GNinja \
      -DCMAKE_INSTALL_PREFIX=/usr \
      -DCMAKE_INSTALL_LIBDIR=lib \
      -DCMAKE_BUILD_TYPE=Release \
      -DSPIRV_WERROR=Off \
      -DBUILD_SHARED_LIBS=ON \
      -DSPIRV_TOOLS_BUILD_STATIC=OFF \
      -DSPIRV-Headers_SOURCE_DIR=${srcdir}/SPIRV-Headers
  ninja

  export CC="gcc -m32"
  export CXX="g++ -m32"
  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"

  cd "${srcdir}"/SPIRV-Tools/build32
  cmake .. \
      -GNinja \
      -DCMAKE_INSTALL_PREFIX=/usr \
      -DCMAKE_INSTALL_LIBDIR=lib32 \
      -DCMAKE_BUILD_TYPE=Release \
      -DSPIRV_WERROR=Off \
      -DBUILD_SHARED_LIBS=ON \
      -DSPIRV_TOOLS_BUILD_STATIC=OFF \
      -DSPIRV-Headers_SOURCE_DIR=${srcdir}/SPIRV-Headers
  ninja
}

package_spirv-tools-tkg-git() {
  provides=("spirv-tools=$pkgver-$pkgrel")
  conflicts=('spirv-tools')

  cd "${srcdir}"/SPIRV-Tools/build64

  DESTDIR="${pkgdir}" ninja install

  install -dm755 "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m644 ../LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/"
}

package_lib32-spirv-tools-tkg-git() {
  provides=("lib32-spirv-tools=$pkgver-$pkgrel")
  conflicts=('lib32-spirv-tools')

  cd "${srcdir}"/SPIRV-Tools/build32

  DESTDIR="${pkgdir}" ninja install

  rm -rf ${pkgdir}/usr/lib
  rm -rf ${pkgdir}/usr/include
  rm -rf ${pkgdir}/usr/bin

  install -dm755 "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m644 ../LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/"
}

function exit_cleanup {

if [ $_NUKR == "true" ]; then
  # Sanitization
  rm -rf "$srcdir"/SPIRV-*
  rm -rf "$srcdir"/*.toolchain
  msg2 'exit cleanup done'
fi

  remove_deps
}

trap exit_cleanup EXIT
