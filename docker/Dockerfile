FROM alpine:3.20

#TODO: Depend on our own libjxl build

ARG IM_GIT_TAG=7.1.1-32

LABEL maintainer="cmahnke@gmail.com"
LABEL org.opencontainers.image.source https://github.com/cmahnke/hdr-tools

ENV BUILD_DEPS="git wget cmake make automake autoconf libtool clang-dev g++ zlib-dev libjpeg-turbo-dev libpng-dev libjxl-dev libavif-dev libwebp-dev openexr-dev libheif-dev libzip-dev libxml2-dev lcms2-dev freetype-dev fontconfig-dev" \
    RUN_DEPS="zlib jpeg libjpeg-turbo libpng libjxl openexr-libopenexr libavif libwebp libheif libzip libxml2 libwebpdecoder libwebpdemux libwebpmux openexr-libopenexrutil openexr-libopenexrcore brotli lcms2 freetype fontconfig ffmpeg" \
    LIBULTRAHDR_GIT_REPO="https://github.com/google/libultrahdr.git" \
    LIBULTRAHDR_GIT_TAG="main" \
    IM_GIT_REPO="https://github.com/ImageMagick/ImageMagick.git" \
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
    cmake -G "Unix Makefiles" -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DUHDR_BUILD_TESTS=1 ../ && \
    make && make install && \
    cd .. && \
# ImageMagick
    git clone --depth 1 --branch $IM_GIT_TAG $IM_GIT_REPO && \
    cd ImageMagick && \
    ./configure --with-uhdr=yes --with-webp=yes --with-jpeg=yes --with-jxl=yes --with-openexr=yes	--with-xml=yes --with-zip=yes --with-zlib=yes --enable-hdri=yes --with-freetype=yes --with-tiff=no && \
    make && make install && \
# Cleanup
    rm -rf $BUILD_DIR  && \
    apk del $BUILD_DEPS && \
    rm -rf /var/cache/apk/* /root/.cache
