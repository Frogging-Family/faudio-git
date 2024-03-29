# Based on AUR pkgbuild from Andrew Whatson <whatson@gmail.com>
# Modified for multilib and userpatches support by: Tk-Glitch <ti3nou at gmail dot com>

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

_NUKR="true" # Cleanups
_lib32="true" # Enable/Disable lib32 package building
_faudio_commit="" # To target a specific version of faudio based on commit id - Leave empty to use latest HEAD

# xWMA impl - set to "ffmpeg" for legacy xWMA support on 7f52bfb66b73cca2522537f45977886958a06324 or older
_xWMA_impl="gst"

_pkgname=('faudio-tkg')
_gitname=FAudio
pkgname=("${_pkgname}-git" )

# lib32
if [ "$_lib32" == "true" ]; then
  pkgname=("${pkgname[@]}" "lib32-${_pkgname}-git")
fi

pkgver=21.04.r0.g398ca11
pkgrel=1
pkgdesc="Accuracy-focused XAudio reimplementation for open platforms"
arch=('i686' 'x86_64')
url='https://github.com/FNA-XNA/FAudio'
license=('Zlib')

makedepends=('git' 'cmake' 'sdl2' 'dbus')
if [ "$_lib32" == "true" ]; then
  makedepends+=(lib32-sdl2 lib32-dbus)
fi

# xWMA support
if [ "$_xWMA_impl" = "ffmpeg" ]; then
  _xWMA_support="-DFFMPEG=ON"
  makedepends+=(ffmpeg)
  if [ "$_lib32" == "true" ]; then
    makedepends+=(lib32-ffmpeg)
  fi
else
  _xWMA_support="-DGSTREAMER=ON"
  makedepends+=(gst-plugins-base-libs)
  if [ "$_lib32" == "true" ]; then
    makedepends+=(lib32-gst-plugins-base-libs)
  fi
fi

if [ -n "${_faudio_commit}" ]; then
  _faudio_commit="#commit=${_faudio_commit}"
  source=("git+https://github.com/FNA-XNA/FAudio.git${_faudio_commit}")
else
  source=(git+https://github.com/FNA-XNA/FAudio.git)
fi

source+=('force-lib32-sdl2.patch'
        'force-lib32-sdl2-legacy.patch')
sha256sums=('SKIP'
            '74a3432a09b1fa70cda254af5b0e04dbdb72f7f082dde7f625a477cacd634975'
            '8454e8342989cdbfb571ff9769baea962576b9c961f166df4bff47c610b7f114')

user_patcher() {
	# To patch the user because all your base are belong to us
	local patches=("$_where"/*."${_userpatch_ext}patch")
	if [ ${#patches[@]} -ge 2 ] || [ -e "${patches[1]}" ]; then
	  if [ "$_user_patches_no_confirm" != "true" ]; then
	    msg2 "Found ${#patches[@]} userpatches for ${_userpatch_target}:"
	    printf '%s\n' "${patches[@]}"
	    read -rp "Do you want to install it/them? - Be careful with that ;)"$'\n> N/y : ' _CONDITION;
	  fi
	  if [ "$_CONDITION" == "y" ] || [ "$_user_patches_no_confirm" == "true" ]; then
	    for _f in "${patches[@]}"; do
	      if [ -e "${_f}" ]; then
	        msg2 "######################################################"
	        msg2 ""
	        msg2 "Applying your own ${_userpatch_target} patch ${_f}"
	        msg2 ""
	        msg2 "######################################################"
	        patch -Np1 < ${_f}
	        echo "Applied your own patch ${_f}" >> $_where/last_build_config.log
	      fi
	    done
	  fi
	fi

	patches=("$_where"/*."${_userpatch_ext}revert")
	if [ ${#patches[@]} -ge 2 ] || [ -e "${patches[1]}" ]; then
	  if [ "$_user_patches_no_confirm" != "true" ]; then
	    msg2 "Found ${#patches[@]} 'to revert' userpatches for ${_userpatch_target}:"
	    printf '%s\n' "${patches[@]}"
	    read -rp "Do you want to install it/them? - Be careful with that ;)"$'\n> N/y : ' _CONDITION;
	  fi
	  if [ "$_CONDITION" == "y" ] || [ "$_user_patches_no_confirm" == "true" ]; then
	    for _f in "${patches[@]}"; do
	      if [ -e "${_f}" ]; then
	        msg2 "######################################################"
	        msg2 ""
	        msg2 "Reverting your own ${_userpatch_target} patch ${_f}"
	        msg2 ""
	        msg2 "######################################################"
	        patch -Np1 -R < ${_f}
	        echo "Reverted your own patch ${_f}" >> $_where/last_build_config.log
	      fi
	    done
	  fi
	fi
}

prepare() {
  cd "$srcdir/${_gitname}"

  # faudio user patches
  _userpatch_target="faudio"
  _userpatch_ext="myfaudio"
  user_patcher

  # Separate dirs for multilib
  mkdir -p "$srcdir/64/build"
  mkdir -p "$srcdir/32/build"

  # Make a copy of src for each target to prevent breakage
  cp -a "$srcdir/${_gitname}"/* "$srcdir/64"
  cp -a "$srcdir/${_gitname}"/* "$srcdir/32"
}

pkgver() {
  cd "$srcdir/${_gitname}"
  git describe --long --tags --always | sed 's/\([^-]*-g\)/r\1/;s/-/./g;s/^v//'
}

build() {
  cd "$srcdir/${_gitname}"
  if git merge-base --is-ancestor 122b635efe75bd8640cd511da139fef2f01219a5 HEAD; then
    _patchver="0"
  elif git merge-base --is-ancestor 3dd4e04153b0a35efd605c1a27a4123a69b08cc0 HEAD; then
    _patchver="1"
  else
    _patchver="2"
  fi

  cd "$srcdir/64/build"

  cmake .. \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_LIBDIR=lib \
    -DCMAKE_INSTALL_INCLUDEDIR=include/FAudio \
    "$_xWMA_support"
  make

  # lib32
  if [ "$_lib32" == "true" ]; then

    # Fix for lib32 SDL2
    cd "$srcdir/32"
    if [ "$_patchver" == "1" ]; then
      patch -p1 -i ../force-lib32-sdl2.patch
    elif [ "$_patchver" == "2" ]; then
      patch -p1 -i ../force-lib32-sdl2-legacy.patch
    fi

    cd "$srcdir/32/build"

    export CC="gcc -m32 -mstackrealign"
    export CXX="g++ -m32 -mstackrealign"
    export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"
    if [ "$_xWMA_impl" = "ffmpeg" ]; then
      _xWMA_support="-DFFMPEG=ON -DFFmpeg_LIBRARY_DIRS=/usr/lib32"
    fi

    cmake .. \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_INSTALL_PREFIX=/usr \
      -DCMAKE_INSTALL_LIBDIR=lib32 \
      -DCMAKE_INSTALL_INCLUDEDIR=include/FAudio \
      "$_xWMA_support"
    make
  fi
}

package_faudio-tkg-git() {
provides=("faudio" "faudio-git")
conflicts=("faudio" "faudio-git")
depends=('sdl2')
if ( cd "$srcdir/${_gitname}" && git merge-base --is-ancestor 7f52bfb66b73cca2522537f45977886958a06324 HEAD ); then
  depends+=(gst-plugins-base-libs)
else
  depends+=(ffmpeg)
fi

  cd "$srcdir/64/build"

  cat>faudio.pc<<'EOF'
prefix=/usr
libdir=${prefix}/lib
includedir=${prefix}/include/FAudio

Name: faudio
Description: Accuracy-focused XAudio reimplementation for open platforms
Version:

Libs: -L${libdir} -lFAudio
Cflags: -I${includedir}
EOF

  make install DESTDIR="${pkgdir}"

  install -D -m644 -t "${pkgdir}/usr/share/licenses/faudio" ../LICENSE
  install -D -m644 -t "${pkgdir}/usr/lib/pkgconfig" faudio.pc
}

package_lib32-faudio-tkg-git() {
provides=("lib32-faudio" "lib32-faudio-git")
conflicts=("lib32-faudio" "lib32-faudio-git")
depends=("${pkgname}" lib32-sdl2)
if ( cd "$srcdir/${_gitname}" && git merge-base --is-ancestor 7f52bfb66b73cca2522537f45977886958a06324 HEAD ); then
  depends+=(lib32-gst-plugins-base-libs)
else
  depends+=(lib32-ffmpeg)
fi

  cd "$srcdir/32/build"

  cat>faudio.pc<<'EOF'
prefix=/usr
libdir=${prefix}/lib32
includedir=${prefix}/include/FAudio

Name: faudio
Description: Accuracy-focused XAudio reimplementation for open platforms
Version:

Libs: -L${libdir} -lFAudio
Cflags: -I${includedir}
EOF

  make install DESTDIR="${pkgdir}"

  rm -r "${pkgdir}/usr/include"

  mkdir -p "${pkgdir}/usr/share/licenses"
  ln -s faudio "${pkgdir}/usr/share/licenses/lib32-faudio"

  install -D -m644 -t "${pkgdir}/usr/lib32/pkgconfig" faudio.pc
}

function exit_cleanup {

if [ $_NUKR == "true" ]; then
  # Sanitization
  rm -rf "$srcdir"/"${_gitname}"
  rm -rf "$srcdir"/64
  rm -rf "$srcdir"/32
  rm -rf "$srcdir"/*.patch
  msg2 'exit cleanup done'
fi

  remove_deps
}

trap exit_cleanup EXIT
