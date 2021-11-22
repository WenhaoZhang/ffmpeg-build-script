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

```bash
$ git clone https://github.com/markus-perl/ffmpeg-build-script.git
$ cd ffmpeg-build-script
$ ./build-ffmpeg --build
```

## Installed packages

### Common tools

* `pkg-config`: https://pkgconfig.freedesktop.org/releases/pkg-config-0.29.2.tar.gz
* `yasm`: https://github.com/yasm/yasm/releases/download/v1.3.0/yasm-1.3.0.tar.gz
* `nasm`: https://www.nasm.us/pub/nasm/releasebuilds/2.15.05/nasm-2.15.05.tar.xz
* `zlib`: https://www.zlib.net/zlib-1.2.11.tar.gz
* `m4`: https://ftp.gnu.org/gnu/m4/m4-1.4.19.tar.gz
* `autoconf`: https://ftp.gnu.org/gnu/autoconf/autoconf-2.71.tar.gz
* `automake`: https://ftp.gnu.org/gnu/automake/automake-1.16.4.tar.gz
* `libtool`: https://ftpmirror.gnu.org/libtool/libtool-2.4.6.tar.gz
* `openssl`: https://www.openssl.org/source/openssl-1.1.1l.tar.gz
* `cmake`: https://cmake.org/files/LatestRelease/cmake-3.22.0.tar.gz

### Supported Codecs

* `x264`: https://code.videolan.org/videolan/x264/-/archive/5db6aa6cab1b146e07b60cc1736a01f21da01154/x264-5db6aa6cab1b146e07b60cc1736a01f21da01154.tar.gz
* `x265`: https://github.com/videolan/x265/archive/Release_3.5.tar.gz
* `libsvtav1`: https://gitlab.com/AOMediaCodec/SVT-AV1/-/archive/v0.8.7/SVT-AV1-v0.8.7.tar.gz 
* `libaom`: https://aomedia.googlesource.com/aom/+archive/287164de79516c25c8c84fd544f67752c170082a.tar.gz
* `librav1e`: https://github.com/xiph/rav1e/archive/refs/tags/v0.5.0.tar.gz (only available if [`cargo` is installed](https://doc.rust-lang.org/cargo/getting-started/installation.html)) 
* `libdav1d`: https://code.videolan.org/videolan/dav1d/-/archive/0.9.2/dav1d-0.9.2.tar.gz (only available if `meson` and `ninja` are installed)
* `libvpx`: https://github.com/webmproject/libvpx/archive/refs/tags/v1.11.0.tar.gz
* `libvmaf`: https://github.com/Netflix/vmaf/archive/refs/tags/v2.3.0.tar.gz

### ffmpeg
* `ffmpeg`: https://github.com/FFmpeg/FFmpeg/archive/refs/heads/release/4.4.tar.gz

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
