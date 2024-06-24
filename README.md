# HDR tools

This Docker image contains ImageMagick for HDR capable image formats, like:
* UltraHDR (`libultrahdr`)
* JXL (`libjxl`)
* JXR (`jxrlib`)
* OpenEXR
* AVIF

Since `libultrahdr` is build from source, all available commands are included as well

`ffmpeg` is also include to create YUV images.

Additionally [`pfstools`](https://pfstools.sourceforge.net/index.html) is provided, at least to certain extend:
GUI (QT) applications MatLab, OpenCV aren't supported. ImageMagick, NetPBM and some tone mapping algorithms aren't supported, since `pfstools` isn't very well supported and those features won't compile with recent dependencies.

# Building

## HDR Tools

```
docker  build --progress=plain -f docker/hdr-tools/Dockerfile .
```

## Python OpenCV

```
docker build -f docker/python-opencv/Dockerfile .
```

# Usage

## Start a container in the current directory

```
docker run -it -v `pwd`:`pwd` ghcr.io/cmahnke/hdr-tools:latest
```

## To enable `libheif`

```
docker run -it --cap-add CAP_SYS_NICE -v `pwd`:`pwd` ghcr.io/cmahnke/hdr-tools:latest
```

## Image information

```
identify -verbose image.ext
```

HDR images are likely to have maximum values larger then either 4096 or 65535, depending on mode for at least one channel, SDR images are likely to be clipped to the maximum.

## Conversion

```
convert udhr:input.jpeg output.avif
```

### EXR / HDR to TIFF

Don't use `-colorspace RGB` since this overexposes the output file

```
magick -define quantum:format=floating-point input.exr output.tiff
```

### Tone mappings / color transfer

* -define uhdr:output-transfer-function=hlg
* -define uhdr:output-transfer-function=pq
* -define uhdr:output-transfer-function=linear

## Tone mapping

```
pfsinexr in.exr |pfstmo_drago03|pfsoutexr out.exr

```

### Notes

* Get a list of possible algorithms - `ls /usr/bin/pfstmo_*`
* `pfstmo_durand02` without arguments clips on high values - it's not suitable for lazy HDR processing.
* `pfstmo_mantiuk08` leaves some sort of halo around indirect did regions
* `pfstmo_reinhard05` quite dark
* `pfstmo_mai11` very light
* `pfstmo_fattal02` good colors, but sharp edges
* `pfstmo_pattanaik00` very gray
* `pfstmo_reinhard02` good colors

### Documentation TODO
* Note on colorspace  (EXR is RGB)
*  -define quantum:format=floating-point
* `ultrahdr_app`
