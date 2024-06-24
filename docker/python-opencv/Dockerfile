ARG TAG=3.12-alpine

FROM python:${TAG}

ARG OPENCV_TAG=4.10.0

LABEL maintainer="cmahnke@gmail.com"
LABEL org.opencontainers.image.source https://github.com/cmahnke/hdr-tools

ENV BUILD_DEPS="build-base cmake wget unzip hdf5-dev protobuf-dev openblas-dev@community libjpeg-turbo-dev libpng-dev tiff-dev libwebp-dev openjpeg-dev libtbb-dev@testing  eigen-dev tesseract-ocr-dev python3-dev linux-headers openexr-dev ffmpeg-dev " \
    RUN_DEPS="hdf5 protobuf openblas libjpeg libpng tiff libwebp openjpeg openjpeg-tools libtbb@testing eigen tesseract-ocr tesseract-ocr-data-por openexr-libopenexr openexr-libopenexrutil openexr-libopenexrcore ffmpeg" \
    BUILD_DIR=/tmp/build

RUN echo "@community http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories && \
    echo "@testing http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
    apk --update upgrade &&\
    apk add --no-cache $RUN_DEPS $BUILD_DEPS $ADDITIONAL_DEPS && \
    mkdir -p $BUILD_DIR && \
    cd $BUILD_DIR && \
    pip install --upgrade pip && pip install 'numpy<2' && \
    wget https://github.com/opencv/opencv/archive/${OPENCV_TAG}.zip -O opencv.zip && \
    wget -q https://github.com/opencv/opencv_contrib/archive/${OPENCV_TAG}.zip -O opencv_contrib.zip && \
    unzip -qq opencv.zip && \
    unzip -qq opencv_contrib.zip && \
    mkdir build && cd build && \
    cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D OPENCV_EXTRA_MODULES_PATH=../opencv_contrib-${OPENCV_TAG}/modules \
        -D EIGEN_INCLUDE_PATH=/usr/include/eigen3 -D OPENCV_ENABLE_NONFREE=ON -D WITH_JPEG=ON -D WITH_PNG=ON \
        -D WITH_TIFF=ON -D WITH_WEBP=ON -D WITH_JASPER=ON -D WITH_EIGEN=ON -D WITH_TBB=ON -D WITH_LAPACK=ON \
        -D WITH_PROTOBUF=ON -D WITH_V4L=OFF -D WITH_GSTREAMER=OFF -D WITH_GTK=OFF -D WITH_QT=OFF -D WITH_CUDA=OFF \
        -D WITH_VTK=OFF -D WITH_OPENEXR=ON -D WITH_FFMPEG=ON -D WITH_OPENCL=OFF -D WITH_OPENNI=OFF -D WITH_XINE=OFF \
        -D WITH_GDAL=OFF -D WITH_IPP=OFF -D BUILD_OPENCV_PYTHON3=ON -D BUILD_OPENCV_PYTHON2=OFF \
        -D BUILD_OPENCV_JAVA=OFF -D BUILD_TESTS=OFF -D BUILD_IPP_IW=OFF -D BUILD_PERF_TESTS=OFF \
        -D BUILD_EXAMPLES=OFF -D BUILD_ANDROID_EXAMPLES=OFF -D BUILD_DOCS=OFF -D BUILD_ITT=OFF \
        -D INSTALL_PYTHON_EXAMPLES=OFF -D INSTALL_C_EXAMPLES=OFF -D INSTALL_TESTS=OFF -D PYTHON3_EXECUTABLE=/usr/local/bin/python \
        -D PYTHON3_INCLUDE_DIR=/usr/local/include/python3.12/ -D PYTHON3_LIBRARY=/usr/local/lib/libpython3.so \
        -D PYTHON_LIBRARY=/usr/local/lib/libpython3.so -D PYTHON3_PACKAGES_PATH=/usr/local/lib/python3.12/site-packages/ \
        -D PYTHON3_NUMPY_INCLUDE_DIRS=/usr/local/lib/python3.12/site-packages/numpy/core/include/ ../opencv-${OPENCV_TAG} && \
    make -j$(nproc) && \
    make install && \
    cd / && \
# Cleanup
    #sed -E 's/@[[:alnum:]]+//g'
    rm -rf $BUILD_DIR  && \
    apk del $(echo $BUILD_DEPS | sed -E 's/@[[:alnum:]]+//g') && \
    rm -rf /var/cache/apk/* /root/.cache
