# syntax = docker/dockerfile:1.3

FROM --platform=${BUILDPLATFORM} ubuntu:20.04
ARG TARGETPLATFORM

# Disable interactive prompts on package installation
ENV DEBIAN_FRONTEND noninteractive

ARG MACOS_SDK_VERSION
ARG XCODE_VERSION
ARG XCODE_PRODUCT_BUILD_VERSION

# Dependencies to get the git sources and go binaries
RUN set -eux && \
	apt-get update && \
	apt-get install -y  --no-install-recommends \
		ca-certificates \
		curl \
		git \
		rsync \
		patch \
		python3-dev \
		libssl-dev \
		lzma-dev \
		libxml2-dev \
		bash \
		\
		# for ld64 -random_uuid support
		uuid-dev \
		\
		tar \
		xz-utils \
	&& \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/*

ARG CMAKE_VERSION=${CMAKE_VERSION_MAJOR}.${CMAKE_VERSION_MINOR}.${CMAKE_VERSION_PATCH}
RUN set -eux && \
	curl -SL "https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz" | tar xzf - -C /usr/local --strip-components=1

ARG LLVM_VERSION
RUN set -eux && \
	curl -SL https://github.com/llvm/llvm-project/releases/download/llvmorg-${LLVM_VERSION}/clang+llvm-${LLVM_VERSION}-x86_64-linux-gnu-ubuntu-16.04.tar.xz | tar xf - -J --strip-components=1 -C /usr

ARG OSXCROSS_COMMIT
RUN set -eux && \
	git clone --depth 1 --branch master --single-branch https://github.com/tpoechtrager/osxcross.git /usr/local/osxcross && \
	git -C /usr/local/osxcross checkout -q "${OSXCROSS_COMMIT}" && \
	rm -rf /usr/local/osxcross/.git

RUN set -eux && \
	curl -o /usr/local/osxcross/tarballs/MacOSX${MACOS_SDK_VERSION}.sdk.tar.xz -SL https://storage.googleapis.com/darwin-sdk/MacOSX${MACOS_SDK_VERSION}-${XCODE_VERSION}-${XCODE_PRODUCT_BUILD_VERSION}.sdk.tar.xz

ENV MACOSX_DEPLOYMENT_TARGET=${MACOS_SDK_VERSION}
# emulate Xcode 12 enable `-Werror=implicit-function-declaration` behavior
ENV OSXCROSS_ENABLE_WERROR_IMPLICIT_FUNCTION_DECLARATION=1

WORKDIR /usr/local/osxcross
# debug for osxcross
ARG OCDEBUG=0
RUN set -eux && \
	CC=/usr/bin/clang CXX=/usr/bin/clang++ UNATTENDED=1 SDK_VERSION=${MACOS_SDK_VERSION} OSX_VERSION_MIN=${MACOS_SDK_VERSION} MACOSX_DEPLOYMENT_TARGET=${MACOS_SDK_VERSION} JOBS="$(nproc)" OCDEBUG=${OCDEBUG} ./build.sh

ENV PATH=/usr/local/osxcross/target/bin:$PATH

# # Get Go
# ENV GOLANG_VERSION 1.17.1
# ENV GOLANG_DOWNLOAD_URL https://golang.org/dl/go$GOLANG_VERSION.linux-amd64.tar.gz
# ENV GOLANG_DOWNLOAD_SHA256 dab7d9c34361dc21ec237d584590d72500652e7c909bf082758fb63064fca0ef
# 
# RUN curl -fsSL "$GOLANG_DOWNLOAD_URL" -o golang.tar.gz \
#     && echo "$GOLANG_DOWNLOAD_SHA256  golang.tar.gz" | sha256sum -c - \
#     && tar -C /usr/local -xzf golang.tar.gz \
#     && rm golang.tar.gz
# 
# ENV PATH /usr/local/go/bin:$PATH
# 
# # Linux and Glibc build dependencies and emulator
# RUN apt-get update && apt-get install -y  --no-install-recommends \
#         bison gawk make python3 \
#         gcc gcc-multilib \
#         gettext texinfo \
#         qemu-user \
#     && apt-get clean \
#     && rm -rf /var/lib/apt/lists/*
# # Cross compilers (install recommended packages to get cross libc-dev)
# RUN apt-get update && apt-get install -y \
#         gcc-aarch64-linux-gnu       gcc-arm-linux-gnueabi     \
#         gcc-mips-linux-gnu          gcc-mips64-linux-gnuabi64 \
#         gcc-mips64el-linux-gnuabi64 gcc-mipsel-linux-gnu      \
#         gcc-powerpc-linux-gnu       gcc-powerpc64-linux-gnu   \
#         gcc-powerpc64le-linux-gnu   gcc-riscv64-linux-gnu     \
#         gcc-s390x-linux-gnu         gcc-sparc64-linux-gnu     \
#     && apt-get clean \
#     && rm -rf /var/lib/apt/lists/*
# 
# # Let the scripts know they are in the docker environment
# ENV GOLANG_SYS_BUILD docker
# WORKDIR /build
# ENTRYPOINT ["go", "run", "linux/mkall.go", "/git/linux", "/git/glibc"]
