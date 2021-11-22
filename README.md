This ffmpeg build script is forked from https://github.com/markus-perl/ffmpeg-build-script and customized to support limited new video codecs. 

build-ffmpeg
==========

The FFmpeg build script provides an easy way to build a **static** FFmpeg on **macOS** and **Linux** with optional **non-free and GPL codecs** (--enable-gpl-and-non-free, see https://ffmpeg.org/legal.html) included.

## Disclaimer And Data Privacy Notice

This script will download different packages with different licenses from various sources, which may track your usage.
These sources are out of control by the developers of this script. Also, this script can create a non-free and unredistributable binary.
By downloading and using this script, you are fully aware of this.

Use this script at your own risk. I maintain this script in my spare time. Please do not file bug reports for systems
other than Debian 10 and macOS 11.x, because I don't have the resources or time to maintain different systems.

## Installation

### Common installation (macOS, Linux)

```bash
$ git clone https://github.com/markus-perl/ffmpeg-build-script.git
$ cd ffmpeg-build-script
$ ./build-ffmpeg --build
```

## Supported Codecs

* `x264`: H.264 Video Codec (MPEG-4 AVC)
* `x265`: H.265 Video Codec (HEVC)
* `libsvtav1`: SVT-AV1 Encoder and Decoder
* `aom`: AV1 Video Codec (Experimental and very slow!)
* `librav1e`: rust based AV1 encoder (only available if [`cargo` is installed](https://doc.rust-lang.org/cargo/getting-started/installation.html)) 
* `libdav1d`: Fastest AV1 decoder developed by the VideoLAN and FFmpeg communities and sponsored by the AOMedia (only available if `meson` and `ninja` are installed)
* `fdk_aac`: Fraunhofer FDK AAC Codec
* `xvidcore`: MPEG-4 video coding standard
* `VP8/VP9/webm`: VP8 / VP9 Video Codec for the WebM video file format
* `mp3`: MPEG-1 or MPEG-2 Audio Layer III
* `ogg`: Free, open container format
* `vorbis`: Lossy audio compression format
* `theora`: Free lossy video compression format
* `opus`: Lossy audio coding format
* `srt`: Secure Reliable Transport
* `webp`: Image format both lossless and lossy

### Apple M1 (Apple Silicon) Support

The script also builds FFmpeg on a new MacBook with an Apple Silicon M1 processor.

### LV2 Plugin Support

If Python is available, the script will build a ffmpeg binary with lv2 plugin support.

## Requirements

### macOS

* XCode 10.x or greater

### Linux

* Debian >= Buster, Ubuntu => Focal Fossa, other Distributions might work too
* A development environment and curl is required

```bash
# Debian and Ubuntu
$ sudo apt install build-essential curl

# Fedora
$ sudo dnf install @development-tools curl
```

### Build in Docker (Linux)

With Docker, FFmpeg can be built reliably without altering the host system. Also, there is no need to have the CUDA SDK
installed outside of the Docker image.

##### Default

If you're running an operating system other than the one above, a completely static build may work. To build a full
statically linked binary inside Docker, just run the following command:

```bash
$ docker build --tag=ffmpeg:default --output type=local,dest=build -f Dockerfile .
```

Build an `export.dockerfile` that copies only what you need from the image you just built as follows. When running,
move the library in the lib to a location where the linker can find it or set the `LD_LIBRARY_PATH`. Since we have
matched the operating system and version, it should work well with dynamic links. If it doesn't work, edit
the `export.dockerfile` and copy the necessary libraries and try again.

```bash
$ docker build --output type=local,dest=build -f export.dockerfile --build-arg DIST=$DIST .
$ ls build
bin lib
$ ls build/bin
ffmpeg ffprobe
$ ls build/lib
libnppc.so.11 libnppicc.so.11 libnppidei.so.11 libnppig.so.11
```

##### Full static version

If you're running an operating system other than the one above, a completely static build may work. To build a full
statically linked binary inside Docker, just run the following command:

```bash
$ sudo -E docker build --tag=ffmpeg:cuda-static --output type=local,dest=build -f full-static.dockerfile .
```

### Run with Docker (macOS, Linux)

You can also run the FFmpeg directly inside a Docker container.

#### Default - Without CUDA (macOS, Linux)

```bash
$ sudo docker build --tag=ffmpeg .
$ sudo docker run ffmpeg -i https://files.coconut.co.s3.amazonaws.com/test.mp4 -f webm -c:v libvpx -c:a libvorbis - > test.mp4
```

### Common build (macOS, Linux)
```bash
$ ./build-ffmpeg --build
```

## Usage
```bash
Usage: build-ffmpeg [OPTIONS]
Options:
  -h, --help                     Display usage information
      --version                  Display version information
  -b, --build                    Starts the build process
      --enable-gpl-and-non-free  Enable non-free codecs  - https://ffmpeg.org/legal.html
      --latest                   Build latest version of dependencies if newer available
  -c, --cleanup                  Remove all working dirs
      --full-static              Complete static build of ffmpeg (eg. glibc, pthreads etc...) **only Linux**
                                 Note: Because of the NSS (Name Service Switch), glibc does not recommend static links.
```
