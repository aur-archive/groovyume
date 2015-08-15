# Maintainer: Matthew Coulson <jmcoulson@gmail.com>

pkgname=groovyume
pkgver=0.154.u0
_patchlevel=`echo $pkgver | cut -f2 -du`
_basever=`echo $pkgver | cut -f1,2 -d.`
pkgrel=3
pkgdesc="GroovyUME is a custom UME build mainly aimed at CRT monitors"
url="http://mamedev.org/"
license=('custom:MAME License')
arch=('i686' 'x86_64')
depends=('sdl-groovy' 'libxinerama' 'sdl_ttf' 'alsa-lib' 'qt4' 'xorg-server')
optdepends=('xf86-video-ati-groovy: for low resolutions and vsync on mode switching'
            'xf86-video-nouveau-groovy: for low resolutions'
            'linux-groovy: for ArcadeVGA 3000 use'
	    'switchres: for creation of custom modelines and EDID binaries')
makedepends=('unzip' 'nasm' 'mesa' 'glu' 'wget' 'python2')
DLAGENTS=('http::/usr/bin/wget -U "Mozilla/5.0 (X11; U; Linux x86_64; en-US; rv:1.9.1.2) Gecko/20090804 Shiretoko/3.5.2" -c -t 3 --waitretry=3 -O %o %u')
install=groovyume.install

for i in `seq 1 ${_patchlevel}`; do
	_patches="${_patches} groovyume-${_basever/./}u${i}_diff.zip::http://mamedev.org/updates/${_basever/./}u${i}_diff.zip"
done

source=("mame${_basever/./}s.zip::http://mamedev.org/downloader.php?file=releases/mame${_basever/./}s.zip"
	"groovyume.sh"
	"extras.tar.gz"
	${_patches}
	"hi_154.txt"
	"0154_groovymame_015b.diff")
md5sums=('f7d29c48456c381a9f63e6de19477485'
         '272e0c41c6a0f48c4e6d64994303cade'
         '420b61240bf5ae11615ba7c6100ee00d'
         '57ce19de150c4f3b26613d0309050772'
         '550ae57689e56245fb04d613d043fcc5')


prepare() {
  cd $srcdir/
  if [ $NOEXTRACT -eq 0 ]; then
    unzip mame.zip
    find . -type f -not -name \*.png | xargs perl -pi -e 's/\r\n?/\n/g'
    for i in `seq 1 ${_patchlevel}`; do
      msg "Patch#$i"
      patch -p0 -E <${_basever/./}u$i.diff
    done
  fi
  patch -p0 -E <${srcdir}/hi_154.txt
  patch -p0 -E <${srcdir}/0154_groovymame_015b.diff
}

build() {
  cd $srcdir/
  make NOWERROR=1 ARCHOPTS=-march=native TARGET=ume PYTHON=python2
  make tools NOWERROR=1 ARCHOPTS=-march=native TARGET=ume PYTHON=python2
}

package() {
  cd $srcdir/
  # Install the groovyume script
  install -Dm755 $srcdir/${pkgname}.sh $pkgdir/usr/bin/${pkgname}

  # Install the applications and the UI font in /usr/share
  install -Dm755 ume $pkgdir/usr/share/${pkgname}/${pkgname} || \
  install -Dm755 ume64 $pkgdir/usr/share/${pkgname}/${pkgname}
  
  install -m755 castool $pkgdir/usr/share/${pkgname}/castool
  install -m755 floptool $pkgdir/usr/share/${pkgname}/floptool
  install -m755 imgtool $pkgdir/usr/share/${pkgname}/imgtool
  install -m755 nltool $pkgdir/usr/share/${pkgname}/nltool
  install -m755 chdman $pkgdir/usr/share/${pkgname}/chdman 
  install -m755 jedutil $pkgdir/usr/share/${pkgname}/jedutil
  install -m755 regrep $pkgdir/usr/share/${pkgname}/regrep
  install -m755 romcmp $pkgdir/usr/share/${pkgname}/romcmp
  install -m755 testkeys $pkgdir/usr/share/${pkgname}/testkeys
  install -m755 src2html $pkgdir/usr/share/${pkgname}/src2html
  install -m755 srcclean $pkgdir/usr/share/${pkgname}/srcclean
  install -m755 ldverify $pkgdir/usr/share/${pkgname}/ldverify
  install -m755 ldresample $pkgdir/usr/share/${pkgname}/ldresample

  # Install the extra bits
  install -d $pkgdir/usr/share/${pkgname}/{artwork,ctrlr,keymaps,shader}
  install -d $pkgdir/usr/share/man/man1
  install -d $pkgdir/usr/share/man/man6
  install -m644 src/osd/sdl/shader/glsl*.*h $pkgdir/usr/share/${pkgname}/shader/
  install -m644 src/osd/sdl/man/*.1* $pkgdir/usr/share/man/man1/
  install -m644 src/osd/sdl/man/*.6* $pkgdir/usr/share/man/man6/

  install -m644 $srcdir/artwork/* $pkgdir/usr/share/${pkgname}/artwork/
  install -m644 $srcdir/ctrlr/* $pkgdir/usr/share/${pkgname}/ctrlr/
  install -m644 src/osd/sdl/keymaps/* $pkgdir/usr/share/${pkgname}/keymaps/

  # Include the license
  install -Dm644 docs/license.txt $pkgdir/usr/share/licenses/${pkgname}/license.txt

  # FS#28203
  sed -i 's|KEYCODE_2_PAD|KEYCODE_2PAD|' $pkgdir/usr/share/groovyume/ctrlr/*.cfg
  sed -i 's|KEYCODE_4_PAD|KEYCODE_4PAD|' $pkgdir/usr/share/groovyume/ctrlr/*.cfg
  sed -i 's|KEYCODE_6_PAD|KEYCODE_6PAD|' $pkgdir/usr/share/groovyume/ctrlr/*.cfg
  sed -i 's|KEYCODE_8_PAD|KEYCODE_8PAD|' $pkgdir/usr/share/groovyume/ctrlr/*.cfg

  find $pkgdir -type f -exec strip {} \;
}
