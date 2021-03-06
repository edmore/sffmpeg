PROJECT(ffmpeg)

CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
INCLUDE(ExternalProject)
INCLUDE_DIRECTORIES(BEFORE ${CMAKE_BINARY_DIR}/include)
LINK_DIRECTORIES(${CMAKE_BINARY_DIR}/lib)
SET(ENV{PATH} ${CMAKE_BINARY_DIR}/bin:$ENV{PATH})
SET(ENV{PKG_CONFIG_PATH} ${CMAKE_BINARY_DIR}/lib/pkgconfig)
SET(CMAKE_VERBOSE_MAKEFILE ON)

EXTERNALPROJECT_ADD(
  yasm
  URL http://www.tortall.net/projects/yasm/releases/yasm-1.3.0.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  z
  URL http://zlib.net/zlib-1.2.8.tar.gz
  CONFIGURE_COMMAND CFLAGS=-fPIC PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  bzip2
  URL http://www.bzip.org/1.0.6/bzip2-1.0.6.tar.gz
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/bzip2-fpic.patch
  CONFIGURE_COMMAND ""
  INSTALL_COMMAND make install PREFIX=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  xz
  URL http://tukaani.org/xz/xz-5.0.7.tar.bz2
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  INSTALL_COMMAND make install PREFIX=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  openssl
  URL http://www.openssl.org/source/openssl-1.0.1i.tar.gz
  CONFIGURE_COMMAND ./config no-shared no-idea no-mdc2 no-rc5 --prefix=${CMAKE_BINARY_DIR}
  BUILD_COMMAND make depend COMMAND make
  INSTALL_COMMAND make install_sw
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  opencore
  URL http://downloads.sourceforge.net/project/opencore-amr/opencore-amr/opencore-amr-0.1.3.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  faac
  URL http://downloads.sourceforge.net/project/faac/faac-src/faac-1.28/faac-1.28.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --without-mp4v2
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  aacplus
  URL http://tipok.org.ua/downloads/media/aacplus/libaacplus/libaacplus-2.0.2.tar.gz
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/libaacplus-limit-nrg-range.patch
  CONFIGURE_COMMAND ./autogen.sh COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  fdkaac
  URL http://downloads.sourceforge.net/project/opencore-amr/fdk-aac/fdk-aac-0.1.3.tar.gz
  PATCH_COMMAND sh ${CMAKE_SOURCE_DIR}/vendor/libaacplus-fdkaac-unclash.sh
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  mp3lame
  URL http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  ogg
  URL http://downloads.xiph.org/releases/ogg/libogg-1.3.2.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  speex
  DEPENDS ogg
  URL http://downloads.xiph.org/releases/speex/speex-1.2rc1.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-sse  --disable-oggtest --with-ogg=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  vorbis
  DEPENDS ogg
  URL http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.4.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --disable-oggtest
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  theora
  DEPENDS ogg
  URL http://downloads.xiph.org/releases/theora/libtheora-1.2.0alpha1.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --disable-oggtest --disable-examples --with-ogg=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  opus
  URL http://downloads.xiph.org/releases/opus/opus-1.1.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --disable-doc
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  xvidcore
  DEPENDS yasm
  URL http://downloads.xvid.org/downloads/xvidcore-1.3.3.tar.gz
  CONFIGURE_COMMAND cd build/generic && PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR}
  BUILD_COMMAND cd build/generic && PATH=$ENV{PATH} make
  INSTALL_COMMAND cd build/generic && make install COMMAND rm -f ${CMAKE_BINARY_DIR}/lib/libxvidcore.so ${CMAKE_BINARY_DIR}/lib/libxvidcore.so.4 ${CMAKE_BINARY_DIR}/lib/libxvidcore.so.4.3
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  x264
  DEPENDS yasm
  GIT_REPOSITORY git://git.videolan.org/x264.git
  UPDATE_COMMAND ""
  # PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/x264-watermark.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --enable-static
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  x265
  DEPENDS yasm
  DOWNLOAD_COMMAND hg clone https://bitbucket.org/multicoreware/x265
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd build/linux && PATH=$ENV{PATH} cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_BINARY_DIR} -DENABLE_CLI:BOOL=OFF -DENABLE_SHARED:BOOL=OFF ../../source
  BUILD_COMMAND cd build/linux && PATH=$ENV{PATH} make
  INSTALL_COMMAND cd build/linux && PATH=$ENV{PATH} make install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  vpx
  DEPENDS yasm
  URL http://webm.googlecode.com/files/libvpx-v1.3.0.tar.bz2
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  rtmp
  DEPENDS z openssl
  GIT_REPOSITORY git://git.ffmpeg.org/rtmpdump
  UPDATE_COMMAND ""
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/rtmpdump-2.4-prefix.patch
  CONFIGURE_COMMAND ""
  BUILD_COMMAND make PREFIX=${CMAKE_BINARY_DIR} SHARED= XCFLAGS=-I${CMAKE_BINARY_DIR}/include XLDFLAGS=-L${CMAKE_BINARY_DIR}/lib XLIBS=-ldl
  INSTALL_COMMAND make PREFIX=${CMAKE_BINARY_DIR} SHARED= install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  freetype
  DEPENDS bzip2
  URL http://download.savannah.gnu.org/releases/freetype/freetype-2.5.3.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static CFLAGS=-fPIC\ -I${CMAKE_BINARY_DIR}/include LDFLAGS=-L${CMAKE_BINARY_DIR}/lib
  INSTALL_COMMAND PATH=$ENV{PATH} make install COMMAND ln -fs ${CMAKE_BINARY_DIR}/include/freetype2 ${CMAKE_BINARY_DIR}/include/freetype2/freetype
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  fribidi
  URL http://fribidi.org/download/fribidi-0.19.6.tar.bz2
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  expat
  URL http://downloads.sourceforge.net/project/expat/expat/2.1.0/expat-2.1.0.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static CFLAGS=-fPIC
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  fontconfig
  DEPENDS freetype expat
  URL http://www.freedesktop.org/software/fontconfig/release/fontconfig-2.11.1.tar.bz2
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static CFLAGS=-fPIC
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  ass
  DEPENDS yasm freetype fribidi fontconfig
  URL https://github.com/libass/libass/releases/download/0.11.2/libass-0.11.2.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  gavl
  URL http://downloads.sourceforge.net/project/gmerlin/gavl/1.4.0/gavl-1.4.0.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static --without-doxygen CFLAGS=-fPIC
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  cairo
# URL http://cairographics.org/releases/cairo-1.12.14.tar.xz
  URL http://helium.ijs.si/cairo-1.12.14.tar.bz2
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static CFLAGS=-fPIC
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  frei0r
  DEPENDS gavl cairo
  URL https://files.dyne.org/frei0r/releases/frei0r-plugins-1.4.tar.gz
  CONFIGURE_COMMAND autoreconf -i COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --enable-static
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  bluray
  GIT_REPOSITORY git://git.videolan.org/libbluray.git
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND autoreconf -i COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  flac
  DEPENDS ogg
# URL http://downloads.xiph.org/releases/flac/flac-1.3.0.tar.xz
  URL http://helium.ijs.si/flac-1.3.0.tar.bz2
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/flac-inline.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  sndfile
  DEPENDS vorbis flac
  URL http://www.mega-nerd.com/libsndfile/files/libsndfile-1.0.25.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  bs2b
  DEPENDS vorbis sndfile
  URL http://downloads.sourceforge.net/project/bs2b/libbs2b/3.1.0/libbs2b-3.1.0.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared CFLAGS=-I${CMAKE_BINARY_DIR}/include LDFLAGS=-L${CMAKE_BINARY_DIR}/lib LIBS=-lvorbis\ -lvorbisenc\ -logg\ -lFLAC\ -lm
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  caca
  GIT_REPOSITORY https://github.com/cacalabs/libcaca
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND ./bootstrap COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --disable-java --disable-x11 --disable-doc
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  cdio
  URL http://ftp.gnu.org/gnu/libcdio/libcdio-0.92.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  cdio-paranoia
  DEPENDS cdio
  URL http://ftp.gnu.org/gnu/libcdio/libcdio-paranoia-10.2+0.90+1.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared CFLAGS=-I${CMAKE_BINARY_DIR}/include
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  raw1394
  URL https://www.kernel.org/pub/linux/libs/ieee1394/libraw1394-2.1.0.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  dc1394
  DEPENDS raw1394
  URL http://downloads.sourceforge.net/project/libdc1394/libdc1394-2/2.2.3/libdc1394-2.2.3.tar.gz
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/dc1394-inline.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared CFLAGS=-I${CMAKE_BINARY_DIR}/include LDFLAGS=-L${CMAKE_BINARY_DIR}/lib LIBS=-lpthread\ -lraw1394
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  flite
  URL http://www.festvox.org/flite/packed/flite-1.4/flite-1.4-release.tar.bz2
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/flite-audio-none.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --with-audio=none
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  gsm
  URL http://libgsm.sourcearchive.com/downloads/1.0.13-4/libgsm_1.0.13.orig.tar.gz
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/gsm-install-root.patch
  CONFIGURE_COMMAND ""
  INSTALL_COMMAND PATH=$ENV{PATH} make INSTALL_ROOT=${CMAKE_BINARY_DIR} install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  avc1394
  DEPENDS raw1394
  URL http://downloads.sourceforge.net/project/libavc1394/libavc1394/libavc1394-0.5.4.tar.gz
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/avc1394-dupl-ref.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  iec61883
  DEPENDS raw1394 avc1394
  URL https://www.kernel.org/pub/linux/libs/ieee1394/libiec61883-1.2.0.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  ilbc
  GIT_REPOSITORY https://github.com/dekkers/libilbc.git
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND autoreconf -i COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  modplug
  GIT_REPOSITORY https://github.com/Konstanty/libmodplug.git
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND autoreconf -i COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  nut
  GIT_REPOSITORY git://git.ffmpeg.org/nut
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND ""
  BUILD_COMMAND cd src/trunk && PATH=$ENV{PATH} make
  INSTALL_COMMAND cd src/trunk && PATH=$ENV{PATH} make prefix=${CMAKE_BINARY_DIR} install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  openjpeg
  URL http://openjpeg.googlecode.com/files/openjpeg-1.5.1.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  orc
  URL http://code.entropywave.com/download/orc/orc-0.4.18.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  schroedinger
  DEPENDS orc
  URL http://diracvideo.org/download/schroedinger/schroedinger-1.0.11.tar.gz
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/schroedinger-configure.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  shine
  GIT_REPOSITORY https://github.com/savonet/shine.git
  UPDATE_COMMAND ""
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/shine-dupl-ref.patch
  CONFIGURE_COMMAND mkdir -p m4 COMMAND autoreconf -i COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  soxr
# URL http://downloads.sourceforge.net/project/soxr/soxr-0.1.1-Source.tar.xz
  URL http://helium.ijs.si/soxr-0.1.1-Source.tar.bz2
  CONFIGURE_COMMAND mkdir -p Release COMMAND cd Release && cmake -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_BINARY_DIR} -DBUILD_SHARED_LIBS:BOOL=OFF ..
  BUILD_COMMAND cd Release && make
  INSTALL_COMMAND cd Release && make install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  twolame
  URL http://downloads.sourceforge.net/project/twolame/twolame/0.3.13/twolame-0.3.13.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  v4l2
  URL http://linuxtv.org/downloads/v4l-utils/v4l-utils-1.5.90.tar.bz2
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/v4l2-compile.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  vidstab
  GIT_REPOSITORY https://github.com/georgmartius/vid.stab.git
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND PATH=$ENV{PATH} cmake -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_BINARY_DIR} -DBUILD_SHARED_LIBS:BOOL=OFF .
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  vo-amrwbenc
  URL http://downloads.sourceforge.net/project/opencore-amr/vo-amrwbenc/vo-amrwbenc-0.1.3.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  wavpack
  URL http://www.wavpack.com/wavpack-4.70.0.tar.bz2
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static --enable-mmx
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  webp
  GIT_REPOSITORY https://chromium.googlesource.com/webm/libwebp
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND autoreconf -i COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  xavs
  DEPENDS yasm
  SVN_REPOSITORY https://svn.code.sf.net/p/xavs/code/trunk
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR}
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  zmq
  URL http://download.zeromq.org/zeromq-4.0.4.tar.gz
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static LIBS=-lstdc++
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  zvbi
  URL http://downloads.sourceforge.net/project/zapping/zvbi/0.2.35/zvbi-0.2.35.tar.bz2
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  ffmpeg
  DEPENDS bzip2 xz yasm opencore faac aacplus fdkaac mp3lame ogg speex vorbis theora opus xvidcore x264 x265 vpx rtmp freetype ass frei0r bluray bs2b caca cdio-paranoia dc1394 flite gsm iec61883 ilbc modplug nut openjpeg schroedinger shine soxr twolame v4l2 vidstab vo-amrwbenc wavpack webp xavs zmq zvbi
  URL http://www.ffmpeg.org/releases/ffmpeg-2.5.4.tar.bz2
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/vendor/ffmpeg-ism-offset.patch
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --datadir=/usr/share/ffmpeg --disable-shared --enable-static --enable-gpl --enable-version3 --enable-nonfree --disable-debug --disable-ffplay --disable-ffserver --enable-runtime-cpudetect --enable-memalign-hack --extra-cflags=-I${CMAKE_BINARY_DIR}/include\ --static --extra-ldflags=-L${CMAKE_BINARY_DIR}/lib\ -static --extra-libs=-lstdc++\ -lexpat\ -ldl\ -lpng\ -lxml2\ -lusb-1.0\ -lraw1394\ -lorc-0.4\ -lv4lconvert\ -ljpeg\ -lncurses\ -ltinfo\ -lgpm\ -lbluray\ -lgomp\ -lrt\ -llzma\ -lz\ -lm --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libfaac --enable-libfdk-aac --enable-libaacplus --enable-libmp3lame --enable-libspeex --enable-libtheora --enable-libvorbis --enable-libx264 --enable-libx265 --enable-libxvid --enable-libvpx --enable-libopus --enable-librtmp --enable-libfreetype --enable-libass --enable-fontconfig --enable-frei0r --enable-libbluray --enable-libbs2b --enable-libcaca --enable-libcdio --enable-libdc1394 --enable-libflite --enable-libgsm --enable-libiec61883 --enable-libilbc --enable-libmodplug --enable-libnut --enable-libopenjpeg --enable-libschroedinger --enable-libshine --enable-libsoxr --enable-libtwolame --enable-libv4l2 --enable-libvidstab --enable-libvo-amrwbenc --enable-libwavpack --enable-libwebp --enable-libxavs --enable-libzmq --enable-libzvbi
  BUILD_COMMAND PATH=$ENV{PATH} make
  INSTALL_COMMAND PATH=$ENV{PATH} make install COMMAND PATH=$ENV{PATH} make install-data DATADIR=/usr/share/ffmpeg
  BUILD_IN_SOURCE 1
)
