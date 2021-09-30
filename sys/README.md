# gcr.io/containerz/golang/sys

Cross-platform container build systems for [golang.org/x/sys/unix][golang.org/x/sys/unix].

From [golang.org/x/sys/unix/README.md][golang.org/x/sys/unix/README.md], the official new build system currently only supports `GOOS == linux`.

This container image solved this limitation with a similar way of [`docker/buildx`][github.com/docker/buildx].

> #### New Build System (currently for `GOOS == "linux"`)
> 
> The new build system uses a Docker container to generate the go files directly
> from source checkouts of the kernel and various system libraries. This means
> that on any platform that supports Docker, all the files using the new build
> system can be generated at once, and generated files will not change based on
> what the person running the scripts has installed on their computer.
>
> The OS specific files for the new build system are located in the `${GOOS}`
> directory, and the build is coordinated by the `${GOOS}/mkall.go` program. When
> the kernel or system library updates, modify the Dockerfile at
> `${GOOS}/Dockerfile` to checkout the new release of the source.
>
> To build all the files under the new build system, you must be on an amd64/Linux
> system and have your GOOS and GOARCH set accordingly. Running `mkall.sh` will
> then generate all of the files for all of the GOOS/GOARCH pairs in the new build
> system. Running `mkall.sh -n` shows the commands that will be run.
> 
> Requirements: bash, go, docker

## Container images

### [gcr.io/containerz/golang/sys][gcr.io/containerz/golang/sys]

Each container image tags based on the macOS SDK version.

| Tag                               | Xcode version        | macOS SDKs      | iOS SDKs        |
|:---------------------------------:|:--------------------:|:---------------:|:---------------:|
| gcr.io/containerz/golang/sys:11.1 |      12.4 12D4e      |  11.1 (20C63)   |  14.4 (18D46)   |
| gcr.io/containerz/golang/sys:11.3 |     13.0 13A233      |  11.3 (20E214)  |  15.0 (19A339)  |
| gcr.io/containerz/golang/sys:12.0 | 13.0 Beta 5 13A5212g | 12.0 (21A5304f) | 15.0 (19A5318g) |

<!-- links -->
[golang.org/x/sys/unix/README.md]: https://github.com/golang/sys/tree/master/unix/README.md
[gcr.io/containerz/golang/sys]: https://console.cloud.google.com/gcr/images/containerz/GLOBAL/golang/sys
[github.com/docker/buildx]: https://github.com/docker/buildx

[golang.org/x/sys/unix]: https://github.com/golang/sys/tree/master/unix
