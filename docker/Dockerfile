ARG LIBJXL_VERSION="latest"

FROM ghcr.io/cmahnke/jpeg-xl-action:${LIBJXL_VERSION}

ARG IM_GIT_TAG=7.1.1-32

LABEL maintainer="cmahnke@gmail.com"
LABEL org.opencontainers.image.source https://github.com/cmahnke/hdr-tools

ENV BUILD_DEPS="git wget cmake make automake autoconf libtool clang-dev g++ zlib-dev libjpeg-turbo-dev libpng-dev libavif-dev libwebp-dev openexr-dev libheif-dev libzip-dev libxml2-dev lcms2-dev freetype-dev fontconfig-dev tiff-dev" \
    RUN_DEPS="zlib jpeg libjpeg-turbo libpng openexr-libopenexr libavif libwebp libheif libzip libxml2 libwebpdecoder libwebpdemux libwebpmux openexr-libopenexrutil openexr-libopenexrcore brotli lcms2 freetype fontconfig ffmpeg libjxl-tools tiff" \
    LIBULTRAHDR_GIT_REPO="https://github.com/google/libultrahdr.git" \
    LIBULTRAHDR_GIT_TAG="main" \
    IM_GIT_REPO="https://github.com/ImageMagick/ImageMagick.git" \
    LIBJXR_GIT_REPO="https://github.com/4creators/jxrlib.git" \
    LIBJXR_GIT_TAG="v2019.10.9" \
    BUILD_DIR=/tmp/build

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories && \
    echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
    apk --update upgrade &&\
    apk add --no-cache $RUN_DEPS $BUILD_DEPS && \
    mkdir -p $BUILD_DIR $DATA_DIR && \
    export CC=clang CXX=clang++ && \
    cd $BUILD_DIR && \
# libultrahdr
    git clone --depth 1 --branch $LIBULTRAHDR_GIT_TAG $LIBULTRAHDR_GIT_REPO && \
    cd libultrahdr && \
    mkdir -p build && cd build && \
    cmake -G "Unix Makefiles" -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DUHDR_BUILD_TESTS=1 -DCMAKE_INSTALL_PREFIX=/usr ../ && \
    make && make install && \
    cd .. && \
# libjxr
    git clone --depth 1 --branch $LIBJXR_GIT_TAG $LIBJXR_GIT_REPO && \
    cd jxrlib && \
    export DIR_INSTALL=/usr && \
    make && make install && \
    unset DIR_INSTALL && \
    cd .. && \
# ImageMagick
    git clone --depth 1 --branch $IM_GIT_TAG $IM_GIT_REPO && \
    cd ImageMagick && \
    ./configure --with-uhdr=yes --with-webp=yes --with-jpeg=yes --with-jxl=yes --with-openexr=yes	--with-xml=yes --with-zip=yes --with-zlib=yes --enable-hdri=yes --with-freetype=yes --with-tiff=yes --prefix=/usr && \
    make && make install && \
# Cleanup
    rm -rf $BUILD_DIR  && \
    apk del $BUILD_DEPS && \
    rm -rf /var/cache/apk/* /root/.cache
