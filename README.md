# HDR tools

This Docker image contains ImageMagick for HDR capable image formats, like:
* UltraHDR (`libultrahdr`)
* JXL (`libjxl`)
* JXR (`jxrlib`)
* OpenEXR
* AVIF

Since `libultrahdr` is build from source, all available commands are included as well

`ffmpeg` is also include to create YUV images.

# Building

```
docker buildx  build --progress=plain -f docker/Dockerfile .
```

# Usage

## Start a container in the current directory

```
docker run -it -v `pwd`:`pwd` ghcr.io/cmahnke/hdr-tools:latest
```

## Image information

```
identify -verbose image.ext
```

## Conversion

```
convert udhr:input.jpeg output.avif
```

### Tone mappings / color transfer

* -define uhdr:output-transfer-function=hlg
* -define uhdr:output-transfer-function=pq
* -define uhdr:output-transfer-function=linear

### Documentation TODO
* Note on colorspace  (EXR is GB)
*  -define quantum:format=floating-point
