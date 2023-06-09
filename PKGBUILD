# Maintainer: Johannes Schindelin <johannes.schindelin@gmx.de>

pkgbase="cv2pdb"
pkgname=("${pkgbase}")
pkgver=0.52
pkgrel=1
pkgdesc="Converter of DWARF debug information to PDB files (msys2)"
arch=('any')
url="https://github.com/rainers/cv2pdb"
license=('Artistic2')

options=('!strip')

source=("${pkgbase}"::"git+https://github.com/rainers/cv2pdb#branch=HEAD")

sha256sums=('SKIP')

die () {
  echo "$*" >&2
  exit 1
}

case "$CARCH" in
i686) WARCH=Win32; OUTDIR=bin/Release;;
x86_64) WARCH=x64; OUTDIR=bin/Release_x64;;
*) die "Unsupported architecture: $CARCH"
esac

pkgver() {
  cd "$srcdir/cv2pdb"
  for p in \
	0.39:3e0d57ca2607de1313060df084f55173100b3596 \
	0.40:c1c515d00cedc8cb7453616bf0da80141b62454f \
	0.41:31189818cbfc6d0bebf82025f3df632a287b61d6 \
	0.42:14eef861769f85b6f32c68ff1898c41d8ac65ef5 \
	0.43:edc8ac2e4ac38f973ebb259a6a75c1ea9db32072 \
	0.44:ab4b0b1946295f9762ec3ed83a7c84cc8b7aa975 \
	0.45:14641495119635e26aabad1dbccc79a6dd1fc94c \
	0.46:3239e14109ac416358e1962932fdd360e724beea
  do
	  git rev-parse --verify --quiet refs/tags/${p%%:*} >/dev/null >&2 ||
		  git tag -m "Missing tag" ${p%%:*} ${p#*:}
  done
  git describe --tags HEAD | tr - . | sed 's/^v//'
}

prepare () {
  cd "$srcdir/cv2pdb"
  sed -i 's/\(<PlatformToolset>\)[^<]*/\1$(DefaultPlatformToolset)/g' src/cv2pdb.vcxproj
}

build() {
  cd "$srcdir/cv2pdb"
  cmd //c '..\..\msbuild\hMSBuild.bat -notamd64 -vsw-version local /p:Configuration=Release /p:Platform='"$WARCH"' src\cv2pdb.vcxproj' ||
  die "MSBuild failed."

  test -z "$SIGNTOOL" ||
  eval "$SIGNTOOL $OUTDIR/cv2pdb.exe"
}

package () {
  cd "$srcdir"/cv2pdb

  install -d "${pkgdir}/${MSYSTEM_PREFIX}/bin"
  install -m755 $OUTDIR/cv2pdb.exe "${pkgdir}/${MSYSTEM_PREFIX}/bin"

  install -d "${pkgdir}/${MSYSTEM_PREFIX}/share/doc/cv2pdb"
  install -m644 CHANGES FEATURES LICENSE README.MD \
    "${pkgdir}/${MSYSTEM_PREFIX}/share/doc/cv2pdb"

  git -C "$srcdir/cv2pdb" clean -dfx
}
