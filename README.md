# HDR tools

This Docker image contains ImageMagick for HDR capable image formats, like:
* UltraHDR (`libultrahdr`)
* JXL (`libjxl`)
* OpenEXR
* AVIF

Since `libultrahdr` is build from source, all available commands are included as well

`ffmpeg` is also include to create YUV images.

# Building

```
docker buildx  build --progress=plain -f docker/Dockerfile .
```
