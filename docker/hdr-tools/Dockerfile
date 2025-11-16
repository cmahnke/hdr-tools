ARG LIBJXL_VERSION="latest"

FROM ghcr.io/cmahnke/jpeg-xl-action:${LIBJXL_VERSION}

ARG IM_GIT_TAG=7.1.2-8
ARG LIBULTRAHDR_GIT_TAG=main
ARG ADDITIONAL_DEPS=""
ARG LIBJXR_GIT_REPO="https://github.com/mircomir/jxrlib.git"
ARG LIBJXR_GIT_TAG="master"
#ARG LIBJXR_GIT_REPO="https://salsa.debian.org/debian-phototools-team/jxrlib.git"
#ARG LIBJXR_GIT_TAG="debian/1.2_git20170615.f752187-5"

LABEL maintainer="cmahnke@gmail.com"
LABEL org.opencontainers.image.source="https://github.com/cmahnke/hdr-tools"

ENV BUILD_DEPS="git wget cmake make automake autoconf libtool clang-dev g++ zlib-dev libjpeg-turbo-dev libpng-dev libavif-dev libwebp-dev openexr-dev libheif-dev libzip-dev libxml2-dev lcms2-dev freetype-dev fontconfig-dev tiff-dev patch gsl-dev libtheora-dev libexif-dev" \
    RUN_DEPS="zlib jpeg libjpeg-turbo libpng openexr-libopenexr libavif libwebp libheif libzip libxml2 libwebpdecoder libwebpdemux libwebpmux openexr-libopenexrutil openexr-libopenexrcore brotli lcms2 freetype fontconfig libjxl-tools tiff libavif-apps exiv2 gsl libtheora libexif ffmpeg" \
    LIBULTRAHDR_GIT_REPO="https://github.com/google/libultrahdr.git" \
    IM_GIT_REPO="https://github.com/ImageMagick/ImageMagick.git" \
    #LIBJXR_GIT_REPO="https://salsa.debian.org/debian-phototools-team/jxrlib.git" \
    #LIBJXR_GIT_TAG="debian/1.2_git20170615.f752187-5" \
    #LIBJXR_GIT_REPO="https://github.com/4creators/jxrlib.git" \
    #LIBJXR_GIT_TAG="v2019.10.9" \
    PFSTOOLS_URL="https://sourceforge.net/projects/pfstools/files/pfstools/2.2.0/pfstools-2.2.0.tgz/download" \
    PFSTOOLS_EXR_PATCH="https://sourceforge.net/p/pfstools/bugs/54/attachment/0001-Prefer-upstream-CMake-Config-Mode-files-for-OpenEXR.patch" \
    BUILD_DIR=/tmp/build

RUN echo "https://dl-cdn.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories && \
    echo "https://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories && \
    echo "@testing http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
    apk --update upgrade &&\
    apk add --no-cache $RUN_DEPS $BUILD_DEPS $ADDITIONAL_DEPS && \
    mkdir -p $BUILD_DIR $DATA_DIR && \
    export CC=clang CXX=clang++ && \
    cd $BUILD_DIR && \
# pfstools
    export PFSTOOLS_FILE=$(basename $(dirname "$PFSTOOLS_URL")) && \
    wget -O "$PFSTOOLS_FILE" "$PFSTOOLS_URL" && \
    tar xzf "$PFSTOOLS_FILE" && \
    cd $(basename "$PFSTOOLS_FILE" .tgz ) && \
    wget $PFSTOOLS_EXR_PATCH && \
    patch < $(basename "$PFSTOOLS_EXR_PATCH") && \
    mkdir -p build && cd build && \
    cmake -DCMAKE_POLICY_VERSION_MINIMUM=3.5 -Wno-dev -G "Unix Makefiles" -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DCMAKE_INSTALL_PREFIX=/usr ../ && \
    make && make install && \
    cd ../.. && \
# libultrahdr
    git clone --depth 1 --branch $LIBULTRAHDR_GIT_TAG $LIBULTRAHDR_GIT_REPO && \
    cd libultrahdr && \
    #sed -i -E 's/8192/32768/g' lib/include/ultrahdr/jpegdecoderhelper.h && \
    mkdir -p build && cd build && \
    cmake -G "Unix Makefiles" -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DUHDR_BUILD_EXAMPLES=1 -DUHDR_ENABLE_INSTALL=1 -DUHDR_BUILD_TESTS=0 -DUHDR_WRITE_XMP=on -DUHDR_MAX_DIMENSION=32768 -DCMAKE_INSTALL_PREFIX=/usr ../ && \
    if [ "$LIBULTRAHDR_GIT_TAG" = "1.0.0" ] ; then \
      echo "libultrahdr 1.0.0 can't be build as shared library!" && \
      exit 1 ; \
    fi && \
    make && make install && \
    cp ultrahdr_app /usr/bin/ && \
    cd ../.. && \
## libjxr
#    git clone --depth 1 --branch $LIBJXR_GIT_TAG $LIBJXR_GIT_REPO && \
#    cd jxrlib && \
#    export DIR_INSTALL=/usr && \
#    make && make install && \
#    unset DIR_INSTALL && \
#    cd .. && \
# ImageMagick
    git clone --depth 1 --branch $IM_GIT_TAG $IM_GIT_REPO && \
    cd ImageMagick && \
    ./configure --with-uhdr=yes --with-webp=yes --with-jpeg=yes --with-jxl=yes --with-openexr=yes	--with-xml=yes --with-zip=yes --with-zlib=yes --enable-hdri=yes --with-freetype=yes --with-tiff=yes --prefix=/usr && \
    make && make install && \
# Cleanup
    rm -rf $BUILD_DIR  && \
    apk del $BUILD_DEPS && \
    rm -rf /var/cache/apk/* /root/.cache
