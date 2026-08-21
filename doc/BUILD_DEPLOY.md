# Polaris-Server Build and Deployment

## Purpose and scope

Polaris-Server is a CMake/Ninja C++ test application suite with native builds, ARM64 Docker builds, and a separate x86 Ubuntu 22.04 Docker server build. The Makefile also wraps CTest, analysis, Doxygen, and remote deployment of cross-built artifacts.

## 1. Prerequisites

- Linux, GNU Make, CMake, Ninja, C++ compiler, `nproc`, and CTest.
- Sibling `Polaris-HAL` and the parent Polaris checkout for shared paths used by CMake and Docker mounts.
- Docker plus ARM64 and AMD64 platform support for cross/server targets; `make docker-install` may use `sudo apt`.
- `clang-format`, `cmake-format`, `cppcheck`, `doxygen`, and Graphviz for optional quality targets.
- `sshpass`, target network access, and valid SSH credentials for `deploy-a`, `deploy-b`, `run-a`, and `run-b`. Override the Makefile defaults safely.

## 2. Setup

```bash
make configure
make docker-image
make docker-image-server
```

Native output is `build/native`; ARM64 Docker output is `build/cross`; the x86 server Docker output is mounted as `Polaris-Server/build/docker`.

## 3. Build and compile

```bash
make build                  # native CMake/Ninja with BUILD_TESTS=ON
make docker-configure
make docker-build           # ARM64/QEMU build
make docker-build-server    # x86 Ubuntu 22.04 server build
```

The default target is `docker-build`. Use `JOBS=N` to control parallelism; Docker builds cap their internal jobs based on host CPU count.

## 4. Test and lint

```bash
make test                   # native CTest
make docker-test            # ARM64 CTest
make docker-test-server     # x86 server CTest
make format
make cppcheck
make doc
```

`format`, `cppcheck`, and `doc` check for their required tools before invoking generated Ninja targets.

## 5. Packaging and artifacts

No CPack/package target is declared for this leaf. Native and cross build artifacts are the `build/native` and `build/cross` `bin/` and `lib/` directories. The server Docker image is `polaris-dev` by default and is a build environment, not a registry publication.

## 6. Deployment, install, and run

```bash
HAL_SSH_USER=nvidia HAL_SSH_PASS='REDACTED' make deploy-a
make deploy-b
make run-a [app] [args...]
make run-b [app] [args...]
make run [app] [args...]   # local native execution
```

Remote deployment is implemented only for the ARM64 cross build: it replaces `/tmp/acu-test` and copies `build/cross/lib` and `build/cross/bin`. The `docker-build-server` path is for x86 server compilation/testing and has no corresponding remote install target. Verify credentials, IPs, ABI, and target access before running these conditional hardware operations.

## 7. Cleanup

```bash
make clean
make docker-clean
make docker-clean-server
```

These remove native, ARM64 Docker, and x86 server Docker build directories respectively; images and ccache volumes remain.

## 8. Verification caveats

Compilation and CTest do not validate deployment to an NVIDIA device or server environment. Manually verify target ABI, libraries, permissions, runtime behavior, and network access. No cloud provisioning, image push, or production service manager is defined.