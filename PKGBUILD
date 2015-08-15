# Maintainer : Rob McCathie <korrode AT gmail>
# Contributor: Charles Bos <charlesbos1 AT gmail>
# Contributor: Iven Hsu <ivenvd AT gmail>

_name=compiz
_origname=compiz-manjaro
pkgname=compiz-manjaro-lts
_series=0.9.11
pkgver=${_series}.2
pkgrel=1
pkgdesc="OpenGL compositing window manager. Includes friendly defaults, GWD theme selector and autostart for Xfce & MATE - LTS version"
arch=('i686' 'x86_64')
url="https://launchpad.net/${_name}"
license=('GPL' 'LGPL' 'MIT')
depends=('boost' 'xorg-server' 'libxcomposite' 'startup-notification' 'librsvg' 'dbus' 'mesa' 'libxslt' 'fuse' 'glibmm' 'libxrender'
         'libwnck' 'pygtk' 'desktop-file-utils' 'pyrex' 'protobuf' 'metacity2' 'glu' 'libsm' 'dconf')
makedepends=('cmake' 'intltool')
#optdepends=()
conflicts=('compiz' 'compiz09-manjaro-test' 'compiz-core-bzr' 'compiz-core-devel' 'compiz-core' 'compiz-gtk-standalone' 'compiz-xfce' 'compiz-mate'
           'compiz-core-mate' 'compiz-fusion-plugins-main' 'compiz-fusion-plugins-extra' 'compiz-fusion-plugins-unsupported' 'compiz-decorator-gtk'
           'compiz-decorator-kde' 'libcompizconfig' 'compizconfig-python' 'compizconfig-backend-gconf' 'compiz-bcop' 'ccsm' "${_origname}")
replaces=('compiz09-manjaro-test')
provides=("${_name}=${pkgver}" "${_name}-core-devel=${pkgver}" "${_origname}=${pkgver}")
source=("${url}/${_series}/${pkgver}/+download/${_name}-${pkgver}.tar.bz2"
        "place-plugin-fix.patch"
        "set-gwd-default.patch"
        "focus-prevention-disable.patch"
        "${_origname}-defaults.patch"
        "${_origname}.gschema.override"
        "compiz-gtk-decorator-theme-selector"
        "compiz-gtk-decorator-theme.desktop"
        "compiz-xfce-autostart"
        "compiz-xfce-autostart.desktop"
        "${_origname}-decoratortheme"
        "${_origname}-decoratortheme.desktop"
        "${_origname}-mate-decoratortheme"
        "${_origname}-mate-decoratortheme.desktop"
        "xfce-theme-greybird.tar.gz::https://github.com/shimmerproject/Greybird/archive/v1.4.tar.gz"
        "save-focused.patch::http://bazaar.launchpad.net/~compiz-team/compiz/0.9.12/diff/3891")
sha1sums=('fe94f4ee392c187679791ffeddc55fb155353f9e'
          '6b8e92ee404601b63ab7847e12232c27c2542891'
          'be585a68eacf93a1064ea914a927623c3e774b95'
          'b74ab025a89419c23d6f01c41e414281e1a2382f'
          '1996419be45490410887385975cbbebfa15a539a'
          '8906c99eba87d5e0832e2b287c251030fab071fc'
          '9600942697b2a5d47129d3495f82f043cd01ea86'
          'f7e1544f8e6987f86c1b2c3e69b70d2accf95bc9'
          'd55d2efd6e90b237e779ca5a636550d48fb380e1'
          'a790b6556695b06af4eff910e7dc409aab85f9a8'
          '36b8c620e00cfd7d9099c02589cab6906f483e53'
          '56146a4fc169f46504eaa86ca240de34c4bb1fd8'
          '8eff3fedffb543960ebfc19b15e95d7f7a90ee50'
          '890b02696130b8275104aaea110b9beb3a162dce'
          'd08974fb99faee36b37ca67b615caeacbccd6984'
          'a340ed92c276feaadcf4853d53ce27851a997225')
install="${_origname}.install"

prepare() {
  cd "${_name}-${pkgver}"
  
  # Save focused window id before changing viewport
  patch -p0 -i "${srcdir}/save-focused.patch"
  
  # Fix broken placement menu in the place plugin
  patch -p1 -i "${srcdir}/place-plugin-fix.patch"

  # Set gtk-window-decorator as default in the Window Decoration plugin
  patch -p1 -i "${srcdir}/set-gwd-default.patch"

  # Set focus prevention level to off which means that new windows will always get focus
  patch -p1 -i "${srcdir}/focus-prevention-disable.patch"
  
  # Manjaro defaults
  patch -p1 -i "${srcdir}/${_origname}-defaults.patch"

  find -type f \( -name 'CMakeLists.txt' -or -name '*.cmake' \) -exec sed -e 's/COMMAND python/COMMAND python2/g' -i {} \;
  find compizconfig/ccsm -type f -exec sed -e 's|^#!.*python|#!/usr/bin/env python2|g' -i {} \;
}

build() {
  cd "${_name}-${pkgver}"
  
  export PYTHON="/usr/bin/python2"
  
  mkdir build; cd build
  
  cmake .. \
    -DCMAKE_BUILD_TYPE="Release" \
    -DCMAKE_INSTALL_PREFIX="/usr" \
    -DPYTHON_INCLUDE_DIR=/usr/include/python2.7 \
    -DPYTHON_LIBRARY=/usr/lib/libpython2.7.so \
    -DQT_QMAKE_EXECUTABLE=qmake-qt4 \
    -DCOMPIZ_DISABLE_SCHEMAS_INSTALL=On \
    -DCOMPIZ_BUILD_WITH_RPATH=Off \
    -DCOMPIZ_PACKAGING_ENABLED=On \
    -DBUILD_GTK=On \
    -DBUILD_METACITY=On \
    -DBUILD_KDE4=Off \
    -DUSE_GCONF=Off \
    -DUSE_GSETTINGS=On \
    -DCOMPIZ_BUILD_TESTING=Off \
    -DCOMPIZ_WERROR=Off \
    -DCOMPIZ_DEFAULT_PLUGINS="composite,opengl,decor,resize,place,move,compiztoolbox,staticswitcher,expo,grid,regex,animation,ccp"
  
  make
}

package() {
  cd "${_name}-${pkgver}/build"
  make DESTDIR="${pkgdir}" install

  # findcompiz_install needs COMPIZ_DESTDIR and install needs DESTDIR
  #make findcompiz_install
  CMAKE_DIR=$(cmake --system-information | grep '^CMAKE_ROOT' | awk -F\" '{print $2}')
  install -dm755 "${pkgdir}${CMAKE_DIR}/Modules/"
  install -m644 ../cmake/FindCompiz.cmake "${pkgdir}${CMAKE_DIR}/Modules/"	

  # Add documentation
  install -dm755 "${pkgdir}/usr/share/doc/compiz/"
  install ../{AUTHORS,NEWS,README} "${pkgdir}/usr/share/doc/compiz/"

  # Add the gsettings schema files manually
  if ls generated/glib-2.0/schemas/ | grep -qm1 .gschema.xml; then
    install -dm755 "${pkgdir}/usr/share/glib-2.0/schemas/" 
    install -m644 generated/glib-2.0/schemas/*.gschema.xml "${pkgdir}/usr/share/glib-2.0/schemas/" 
  fi 

  # Remove GConf schemas
  rm -rv "${pkgdir}/usr/share/gconf/"
  
  ## Manjaro stuff
  # Add Manjaro dconf/gsettings schema override file
  install -Dm644 "${srcdir}/${_origname}.gschema.override" "${pkgdir}/usr/share/glib-2.0/schemas/${_origname}.gschema.override"
  # Make ccsm icon appear in Xfce settings manager
  sed -i 's|Categories=Compiz;Settings;DesktopSettings;|Categories=Compiz;Settings;DesktopSettings;X-XFCE-SettingsDialog;X-XFCE-OtherSettings;|' "${pkgdir}/usr/share/applications/ccsm.desktop"
  # Install GWD theme selector app
  install -Dm755 "${srcdir}/compiz-gtk-decorator-theme-selector" "${pkgdir}/usr/bin/compiz-gtk-decorator-theme-selector"
  install -Dm644 "${srcdir}/compiz-gtk-decorator-theme.desktop" "${pkgdir}/usr/share/applications/compiz-gtk-decorator-theme.desktop"
  # Place autostart/theme scripts/enablers
  install -Dm755 "${srcdir}/compiz-xfce-autostart" "${pkgdir}/usr/bin/compiz-xfce-autostart"
  install -Dm644 "${srcdir}/compiz-xfce-autostart.desktop" "${pkgdir}/etc/xdg/autostart/compiz-xfce-autostart.desktop"
  install -Dm755 "${srcdir}/${_origname}-decoratortheme" "${pkgdir}/usr/bin/${_origname}-decoratortheme"
  install -Dm644 "${srcdir}/${_origname}-decoratortheme.desktop" "${pkgdir}/etc/xdg/autostart/${_origname}-decoratortheme.desktop"
  install -Dm755 "${srcdir}/${_origname}-mate-decoratortheme" "${pkgdir}/usr/bin/${_origname}-mate-decoratortheme"
  install -Dm644 "${srcdir}/${_origname}-mate-decoratortheme.desktop" "${pkgdir}/etc/xdg/autostart/${_origname}-mate-decoratortheme.desktop"
  
  # greybird window decorations
  cd "${srcdir}/Greybird-1.4"
  mkdir -p "${pkgdir}/usr/share/themes/compiz-xfce-greybird"
  cp LICENSE* "${pkgdir}/usr/share/themes/compiz-xfce-greybird"
  cp -r metacity-1/ "${pkgdir}/usr/share/themes/compiz-xfce-greybird"
  rm "${pkgdir}/usr/share/themes/compiz-xfce-greybird/metacity-1/metacity-theme-3.xml"
  sed -i 's|Greybird|compiz-xfce-greybird|' "${pkgdir}/usr/share/themes/compiz-xfce-greybird/metacity-1/metacity-theme-2.xml"
}

